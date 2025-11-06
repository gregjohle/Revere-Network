# Revere Network - Final Database Schema

## Schema Decisions Based on User Input

1. **Usernames**: Case-insensitive (lowercase stored, display version preserved)
2. **Character Limits**: 500 characters for posts and comments
3. **Media Support**: Include image/video uploads in MVP
4. **Additional Features**: Notification system, Edit history tracking, Private/protected accounts

---

## Complete Database Schema

### 1. Profiles Table

```sql
CREATE TABLE profiles (
  id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  username VARCHAR(30) UNIQUE NOT NULL,
  username_lower VARCHAR(30) UNIQUE NOT NULL, -- For case-insensitive lookups
  display_name VARCHAR(100),
  bio TEXT,
  avatar_url TEXT,
  is_private BOOLEAN NOT NULL DEFAULT FALSE,
  is_verified BOOLEAN NOT NULL DEFAULT FALSE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

  CONSTRAINT username_length CHECK (char_length(username) >= 3 AND char_length(username) <= 30),
  CONSTRAINT username_format CHECK (username ~ '^[a-zA-Z0-9_]+$')
);

-- Indexes
CREATE INDEX idx_profiles_username_lower ON profiles(username_lower);
CREATE INDEX idx_profiles_username ON profiles(username);

-- Trigger to automatically set username_lower
CREATE OR REPLACE FUNCTION set_username_lower()
RETURNS TRIGGER AS $$
BEGIN
  NEW.username_lower = LOWER(NEW.username);
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER set_username_lower_trigger
  BEFORE INSERT OR UPDATE OF username ON profiles
  FOR EACH ROW EXECUTE FUNCTION set_username_lower();
```

---

### 2. Posts Table

```sql
CREATE TABLE posts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  content TEXT NOT NULL,
  media_urls TEXT[], -- Array of media URLs from Supabase Storage
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  edited_at TIMESTAMPTZ, -- Set when post is edited
  is_deleted BOOLEAN NOT NULL DEFAULT FALSE,

  CONSTRAINT content_length CHECK (char_length(content) > 0 AND char_length(content) <= 500),
  CONSTRAINT content_or_media CHECK (char_length(content) > 0 OR media_urls IS NOT NULL)
);

-- Indexes
CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_posts_created_at ON posts(created_at DESC);
CREATE INDEX idx_posts_user_created ON posts(user_id, created_at DESC);
CREATE INDEX idx_posts_not_deleted ON posts(is_deleted) WHERE is_deleted = FALSE;
```

---

### 3. Post Edit History Table

```sql
CREATE TABLE post_edit_history (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  post_id UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
  content TEXT NOT NULL,
  media_urls TEXT[],
  edited_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  edited_by UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE
);

-- Index for retrieving post history
CREATE INDEX idx_post_edit_history_post_id ON post_edit_history(post_id, edited_at DESC);

-- Trigger to save edit history when post is updated
CREATE OR REPLACE FUNCTION save_post_edit_history()
RETURNS TRIGGER AS $$
BEGIN
  -- Only save if content or media changed
  IF OLD.content != NEW.content OR OLD.media_urls != NEW.media_urls THEN
    INSERT INTO post_edit_history (post_id, content, media_urls, edited_by)
    VALUES (OLD.id, OLD.content, OLD.media_urls, NEW.user_id);

    NEW.edited_at = NOW();
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER post_edit_history_trigger
  BEFORE UPDATE ON posts
  FOR EACH ROW
  WHEN (OLD.content IS DISTINCT FROM NEW.content OR OLD.media_urls IS DISTINCT FROM NEW.media_urls)
  EXECUTE FUNCTION save_post_edit_history();
```

---

### 4. Comments Table

```sql
CREATE TABLE comments (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  post_id UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
  user_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  is_deleted BOOLEAN NOT NULL DEFAULT FALSE,

  CONSTRAINT comment_content_length CHECK (char_length(content) > 0 AND char_length(content) <= 500)
);

-- Indexes
CREATE INDEX idx_comments_post_id ON comments(post_id, created_at ASC);
CREATE INDEX idx_comments_user_id ON comments(user_id);
```

---

### 5. Post Reactions Table

```sql
CREATE TABLE post_reactions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  post_id UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
  user_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  reaction_type VARCHAR(10) NOT NULL CHECK (reaction_type IN ('like', 'dislike')),
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

  UNIQUE(post_id, user_id)
);

-- Indexes
CREATE INDEX idx_post_reactions_post_id ON post_reactions(post_id);
CREATE INDEX idx_post_reactions_user_id ON post_reactions(user_id);
CREATE INDEX idx_post_reactions_type_post ON post_reactions(reaction_type, post_id);
```

---

### 6. Comment Reactions Table

```sql
CREATE TABLE comment_reactions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  comment_id UUID NOT NULL REFERENCES comments(id) ON DELETE CASCADE,
  user_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  reaction_type VARCHAR(10) NOT NULL CHECK (reaction_type IN ('like', 'dislike')),
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

  UNIQUE(comment_id, user_id)
);

-- Indexes
CREATE INDEX idx_comment_reactions_comment_id ON comment_reactions(comment_id);
CREATE INDEX idx_comment_reactions_user_id ON comment_reactions(user_id);
```

---

### 7. Follows Table

```sql
CREATE TABLE follows (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  follower_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  following_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  status VARCHAR(20) NOT NULL DEFAULT 'accepted' CHECK (status IN ('pending', 'accepted')),
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

  UNIQUE(follower_id, following_id),
  CHECK (follower_id != following_id)
);

-- Indexes
CREATE INDEX idx_follows_following_id ON follows(following_id);
CREATE INDEX idx_follows_follower_id ON follows(follower_id);
CREATE INDEX idx_follows_status ON follows(status);

-- Trigger to set status based on account privacy
CREATE OR REPLACE FUNCTION set_follow_status()
RETURNS TRIGGER AS $$
DECLARE
  is_private_account BOOLEAN;
BEGIN
  SELECT is_private INTO is_private_account
  FROM profiles
  WHERE id = NEW.following_id;

  IF is_private_account THEN
    NEW.status = 'pending';
  ELSE
    NEW.status = 'accepted';
  END IF;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER set_follow_status_trigger
  BEFORE INSERT ON follows
  FOR EACH ROW EXECUTE FUNCTION set_follow_status();
```

---

### 8. Notifications Table

```sql
CREATE TABLE notifications (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  recipient_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  actor_id UUID REFERENCES profiles(id) ON DELETE CASCADE, -- NULL for system notifications
  notification_type VARCHAR(50) NOT NULL CHECK (
    notification_type IN (
      'follow',
      'follow_request',
      'follow_accepted',
      'post_like',
      'post_dislike',
      'comment',
      'comment_like',
      'comment_dislike',
      'mention',
      'system'
    )
  ),
  post_id UUID REFERENCES posts(id) ON DELETE CASCADE,
  comment_id UUID REFERENCES comments(id) ON DELETE CASCADE,
  is_read BOOLEAN NOT NULL DEFAULT FALSE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

  -- Prevent duplicate notifications
  UNIQUE(recipient_id, actor_id, notification_type, post_id, comment_id)
);

-- Indexes
CREATE INDEX idx_notifications_recipient ON notifications(recipient_id, created_at DESC);
CREATE INDEX idx_notifications_unread ON notifications(recipient_id, is_read) WHERE is_read = FALSE;
```

---

### 9. Materialized View: Post Popularity

```sql
CREATE MATERIALIZED VIEW post_popularity AS
SELECT
  p.id AS post_id,
  p.user_id,
  p.content,
  p.media_urls,
  p.created_at,
  p.updated_at,
  p.edited_at,
  COUNT(CASE WHEN pr.reaction_type = 'like' THEN 1 END) AS like_count,
  COUNT(CASE WHEN pr.reaction_type = 'dislike' THEN 1 END) AS dislike_count,
  COUNT(CASE WHEN pr.reaction_type = 'like' THEN 1 END) -
    COUNT(CASE WHEN pr.reaction_type = 'dislike' THEN 1 END) AS popularity_score,
  COUNT(DISTINCT c.id) AS comment_count
FROM posts p
LEFT JOIN post_reactions pr ON p.id = pr.post_id
LEFT JOIN comments c ON p.id = c.post_id AND c.is_deleted = FALSE
WHERE p.is_deleted = FALSE
GROUP BY p.id, p.user_id, p.content, p.media_urls, p.created_at, p.updated_at, p.edited_at;

-- Indexes
CREATE INDEX idx_post_popularity_score ON post_popularity(popularity_score DESC);
CREATE INDEX idx_post_popularity_likes ON post_popularity(like_count DESC);
CREATE INDEX idx_post_popularity_dislikes ON post_popularity(dislike_count DESC);
```

---

## Row Level Security Policies

### Profiles

```sql
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Public profiles viewable by everyone, private profiles only by approved followers
CREATE POLICY "Public profiles viewable by everyone"
  ON profiles FOR SELECT
  USING (
    is_private = FALSE
    OR id = auth.uid()
    OR EXISTS (
      SELECT 1 FROM follows
      WHERE following_id = profiles.id
      AND follower_id = auth.uid()
      AND status = 'accepted'
    )
  );

-- Users can insert their own profile
CREATE POLICY "Users can insert their own profile"
  ON profiles FOR INSERT
  WITH CHECK (auth.uid() = id);

-- Users can update their own profile
CREATE POLICY "Users can update their own profile"
  ON profiles FOR UPDATE
  USING (auth.uid() = id)
  WITH CHECK (auth.uid() = id);
```

### Posts

```sql
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;

-- Posts viewable based on author's privacy settings
CREATE POLICY "Posts viewable based on privacy"
  ON posts FOR SELECT
  USING (
    is_deleted = FALSE
    AND (
      -- Public account posts
      EXISTS (SELECT 1 FROM profiles WHERE id = posts.user_id AND is_private = FALSE)
      -- Own posts
      OR user_id = auth.uid()
      -- Posts from private accounts user follows
      OR EXISTS (
        SELECT 1 FROM follows
        WHERE following_id = posts.user_id
        AND follower_id = auth.uid()
        AND status = 'accepted'
      )
    )
  );

-- Authenticated users can create posts
CREATE POLICY "Authenticated users can create posts"
  ON posts FOR INSERT
  TO authenticated
  WITH CHECK (auth.uid() = user_id);

-- Users can update their own posts
CREATE POLICY "Users can update their own posts"
  ON posts FOR UPDATE
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);

-- Users can delete their own posts
CREATE POLICY "Users can delete their own posts"
  ON posts FOR DELETE
  USING (auth.uid() = user_id);
```

### Post Edit History

```sql
ALTER TABLE post_edit_history ENABLE ROW LEVEL SECURITY;

-- Edit history viewable same as posts
CREATE POLICY "Edit history viewable with post"
  ON post_edit_history FOR SELECT
  USING (
    EXISTS (
      SELECT 1 FROM posts
      WHERE posts.id = post_edit_history.post_id
      AND (
        posts.user_id = auth.uid()
        OR EXISTS (SELECT 1 FROM profiles WHERE id = posts.user_id AND is_private = FALSE)
        OR EXISTS (
          SELECT 1 FROM follows
          WHERE following_id = posts.user_id
          AND follower_id = auth.uid()
          AND status = 'accepted'
        )
      )
    )
  );
```

### Comments

```sql
ALTER TABLE comments ENABLE ROW LEVEL SECURITY;

-- Comments viewable if post is viewable
CREATE POLICY "Comments viewable with post"
  ON comments FOR SELECT
  USING (
    is_deleted = FALSE
    AND EXISTS (
      SELECT 1 FROM posts
      WHERE posts.id = comments.post_id
      AND posts.is_deleted = FALSE
      AND (
        EXISTS (SELECT 1 FROM profiles WHERE id = posts.user_id AND is_private = FALSE)
        OR posts.user_id = auth.uid()
        OR EXISTS (
          SELECT 1 FROM follows
          WHERE following_id = posts.user_id
          AND follower_id = auth.uid()
          AND status = 'accepted'
        )
      )
    )
  );

-- Authenticated users can create comments on viewable posts
CREATE POLICY "Users can comment on viewable posts"
  ON comments FOR INSERT
  TO authenticated
  WITH CHECK (
    auth.uid() = user_id
    AND EXISTS (
      SELECT 1 FROM posts
      WHERE posts.id = comments.post_id
      AND (
        EXISTS (SELECT 1 FROM profiles WHERE id = posts.user_id AND is_private = FALSE)
        OR posts.user_id = auth.uid()
        OR EXISTS (
          SELECT 1 FROM follows
          WHERE following_id = posts.user_id
          AND follower_id = auth.uid()
          AND status = 'accepted'
        )
      )
    )
  );

-- Users can update their own comments
CREATE POLICY "Users can update their own comments"
  ON comments FOR UPDATE
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);
```

### Post Reactions

```sql
ALTER TABLE post_reactions ENABLE ROW LEVEL SECURITY;

-- Reactions viewable by everyone
CREATE POLICY "Reactions viewable by everyone"
  ON post_reactions FOR SELECT
  USING (true);

-- Users can react to viewable posts
CREATE POLICY "Users can react to viewable posts"
  ON post_reactions FOR INSERT
  TO authenticated
  WITH CHECK (
    auth.uid() = user_id
    AND EXISTS (
      SELECT 1 FROM posts
      WHERE posts.id = post_reactions.post_id
      AND posts.is_deleted = FALSE
      AND (
        EXISTS (SELECT 1 FROM profiles WHERE id = posts.user_id AND is_private = FALSE)
        OR posts.user_id = auth.uid()
        OR EXISTS (
          SELECT 1 FROM follows
          WHERE following_id = posts.user_id
          AND follower_id = auth.uid()
          AND status = 'accepted'
        )
      )
    )
  );

-- Users can update their own reactions
CREATE POLICY "Users can update their own reactions"
  ON post_reactions FOR UPDATE
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);

-- Users can delete their own reactions
CREATE POLICY "Users can delete their own reactions"
  ON post_reactions FOR DELETE
  USING (auth.uid() = user_id);
```

### Comment Reactions

```sql
ALTER TABLE comment_reactions ENABLE ROW LEVEL SECURITY;

-- Similar policies to post reactions
CREATE POLICY "Comment reactions viewable by everyone"
  ON comment_reactions FOR SELECT
  USING (true);

CREATE POLICY "Users can react to viewable comments"
  ON comment_reactions FOR INSERT
  TO authenticated
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update their own comment reactions"
  ON comment_reactions FOR UPDATE
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can delete their own comment reactions"
  ON comment_reactions FOR DELETE
  USING (auth.uid() = user_id);
```

### Follows

```sql
ALTER TABLE follows ENABLE ROW LEVEL SECURITY;

-- Follows viewable by everyone (for public accounts)
CREATE POLICY "Follows viewable for public accounts"
  ON follows FOR SELECT
  USING (
    status = 'accepted'
    AND (
      EXISTS (SELECT 1 FROM profiles WHERE id = follows.following_id AND is_private = FALSE)
      OR following_id = auth.uid()
      OR follower_id = auth.uid()
    )
  );

-- Users can follow others
CREATE POLICY "Users can follow others"
  ON follows FOR INSERT
  TO authenticated
  WITH CHECK (auth.uid() = follower_id);

-- Users can accept follow requests to them
CREATE POLICY "Users can accept follow requests"
  ON follows FOR UPDATE
  USING (auth.uid() = following_id)
  WITH CHECK (auth.uid() = following_id);

-- Users can unfollow
CREATE POLICY "Users can unfollow"
  ON follows FOR DELETE
  USING (auth.uid() = follower_id);
```

### Notifications

```sql
ALTER TABLE notifications ENABLE ROW LEVEL SECURITY;

-- Users can view their own notifications
CREATE POLICY "Users can view own notifications"
  ON notifications FOR SELECT
  USING (auth.uid() = recipient_id);

-- System can create notifications (via service role)
CREATE POLICY "System can create notifications"
  ON notifications FOR INSERT
  WITH CHECK (true);

-- Users can update their own notifications (mark as read)
CREATE POLICY "Users can update own notifications"
  ON notifications FOR UPDATE
  USING (auth.uid() = recipient_id)
  WITH CHECK (auth.uid() = recipient_id);

-- Users can delete their own notifications
CREATE POLICY "Users can delete own notifications"
  ON notifications FOR DELETE
  USING (auth.uid() = recipient_id);
```

---

## Database Functions

### Create Notification Function

```sql
CREATE OR REPLACE FUNCTION create_notification(
  p_recipient_id UUID,
  p_actor_id UUID,
  p_type VARCHAR,
  p_post_id UUID DEFAULT NULL,
  p_comment_id UUID DEFAULT NULL
)
RETURNS UUID AS $$
DECLARE
  notification_id UUID;
BEGIN
  -- Don't notify users of their own actions
  IF p_recipient_id = p_actor_id THEN
    RETURN NULL;
  END IF;

  INSERT INTO notifications (recipient_id, actor_id, notification_type, post_id, comment_id)
  VALUES (p_recipient_id, p_actor_id, p_type, p_post_id, p_comment_id)
  ON CONFLICT DO NOTHING
  RETURNING id INTO notification_id;

  RETURN notification_id;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

### Notification Triggers

```sql
-- Trigger for follow notifications
CREATE OR REPLACE FUNCTION notify_on_follow()
RETURNS TRIGGER AS $$
BEGIN
  IF NEW.status = 'accepted' THEN
    PERFORM create_notification(
      NEW.following_id,
      NEW.follower_id,
      'follow',
      NULL,
      NULL
    );
  ELSIF NEW.status = 'pending' THEN
    PERFORM create_notification(
      NEW.following_id,
      NEW.follower_id,
      'follow_request',
      NULL,
      NULL
    );
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER follow_notification_trigger
  AFTER INSERT OR UPDATE ON follows
  FOR EACH ROW EXECUTE FUNCTION notify_on_follow();

-- Trigger for post reaction notifications
CREATE OR REPLACE FUNCTION notify_on_post_reaction()
RETURNS TRIGGER AS $$
DECLARE
  post_author UUID;
BEGIN
  SELECT user_id INTO post_author FROM posts WHERE id = NEW.post_id;

  PERFORM create_notification(
    post_author,
    NEW.user_id,
    CASE WHEN NEW.reaction_type = 'like' THEN 'post_like' ELSE 'post_dislike' END,
    NEW.post_id,
    NULL
  );

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER post_reaction_notification_trigger
  AFTER INSERT ON post_reactions
  FOR EACH ROW EXECUTE FUNCTION notify_on_post_reaction();

-- Trigger for comment notifications
CREATE OR REPLACE FUNCTION notify_on_comment()
RETURNS TRIGGER AS $$
DECLARE
  post_author UUID;
BEGIN
  SELECT user_id INTO post_author FROM posts WHERE id = NEW.post_id;

  PERFORM create_notification(
    post_author,
    NEW.user_id,
    'comment',
    NEW.post_id,
    NEW.id
  );

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER comment_notification_trigger
  AFTER INSERT ON comments
  FOR EACH ROW EXECUTE FUNCTION notify_on_comment();

-- Trigger for comment reaction notifications
CREATE OR REPLACE FUNCTION notify_on_comment_reaction()
RETURNS TRIGGER AS $$
DECLARE
  comment_author UUID;
  comment_post_id UUID;
BEGIN
  SELECT user_id, post_id INTO comment_author, comment_post_id
  FROM comments WHERE id = NEW.comment_id;

  PERFORM create_notification(
    comment_author,
    NEW.user_id,
    CASE WHEN NEW.reaction_type = 'like' THEN 'comment_like' ELSE 'comment_dislike' END,
    comment_post_id,
    NEW.comment_id
  );

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER comment_reaction_notification_trigger
  AFTER INSERT ON comment_reactions
  FOR EACH ROW EXECUTE FUNCTION notify_on_comment_reaction();
```

---

## Supabase Storage Buckets

### Media Storage Setup

```sql
-- Create storage bucket for user avatars
INSERT INTO storage.buckets (id, name, public)
VALUES ('avatars', 'avatars', true);

-- Create storage bucket for post media
INSERT INTO storage.buckets (id, name, public)
VALUES ('post-media', 'post-media', true);

-- RLS for avatars bucket
CREATE POLICY "Avatar images are publicly accessible"
  ON storage.objects FOR SELECT
  USING (bucket_id = 'avatars');

CREATE POLICY "Users can upload their own avatar"
  ON storage.objects FOR INSERT
  WITH CHECK (
    bucket_id = 'avatars'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );

CREATE POLICY "Users can update their own avatar"
  ON storage.objects FOR UPDATE
  USING (
    bucket_id = 'avatars'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );

-- RLS for post-media bucket
CREATE POLICY "Post media is publicly accessible"
  ON storage.objects FOR SELECT
  USING (bucket_id = 'post-media');

CREATE POLICY "Authenticated users can upload post media"
  ON storage.objects FOR INSERT
  TO authenticated
  WITH CHECK (bucket_id = 'post-media');
```

---

## Setup Order

1. Create all tables in this order:
   - profiles
   - posts
   - post_edit_history
   - comments
   - post_reactions
   - comment_reactions
   - follows
   - notifications

2. Create all triggers and functions

3. Create materialized view

4. Enable RLS and create policies for all tables

5. Set up Supabase Storage buckets

6. Create scheduled job to refresh materialized view every 5 minutes

---

## Summary of Changes from Initial Design

1. Added `username_lower` column and trigger for case-insensitive usernames
2. Increased character limit to 500 for posts and comments
3. Added `media_urls` array column to posts table
4. Created `post_edit_history` table with automatic trigger
5. Added `comment_reactions` table for liking/disliking comments
6. Added `is_private` field to profiles and `status` field to follows
7. Created comprehensive `notifications` table with triggers
8. Added Supabase Storage bucket configuration
9. Enhanced RLS policies to respect private accounts
10. Created notification generation functions and triggers
