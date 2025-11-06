# Revere Network - Story Cards

## Project Overview
Twitter/X clone built with React and Supabase featuring posts, comments, reactions, follows, notifications, private accounts, and edit history.

---

## Epic 1: Project Foundation & Database Setup

### Story Card: DB-001 - Supabase Project Configuration

**Priority:** High
**Story Points:** 2
**Sprint:** Sprint 1
**Status:** Todo
**Branch:** N/A (User handles)
**Assigned To:** User

#### User Story
As a developer, I need a properly configured Supabase project so that I can build the application with a secure backend.

#### Description
Set up the Supabase project with all required database tables, RLS policies, triggers, functions, and storage buckets according to the final schema.

#### Acceptance Criteria
- Given the Supabase dashboard is accessible
  When I create a new project
  Then the project should be configured with authentication enabled

- Given the final database schema document
  When I execute all SQL commands in order
  Then all tables, indexes, triggers, and functions should be created successfully

- Given the storage requirements
  When I configure storage buckets
  Then 'avatars' and 'post-media' buckets should exist with proper RLS policies

- Given all tables are created
  When I enable RLS on each table
  Then all RLS policies should be active and tested

- Given the materialized view is created
  When I set up a scheduled refresh
  Then the view should refresh every 5 minutes automatically

#### Technical Requirements
- Follow the setup order in `/Users/greg/AI-Playground/revere-network-schema-final.md`
- Test RLS policies with different user roles
- Verify foreign key constraints work correctly
- Test triggers fire properly on data changes
- Confirm storage bucket policies allow correct access

#### Design Decisions
- Using case-insensitive usernames with `username_lower` trigger
- 500 character limit for posts and comments
- Soft deletes for posts and comments
- Automatic notification creation via triggers

#### Implementation Notes
- Execute SQL in the Supabase SQL editor
- Test each section before proceeding to the next
- Keep Supabase project URL and anon key for frontend configuration
- Note the service role key securely (needed for admin operations)

#### Testing Checklist
- [ ] All tables created without errors
- [ ] All indexes created successfully
- [ ] All triggers fire on appropriate actions
- [ ] RLS policies enforce correct permissions
- [ ] Storage buckets accessible with correct permissions
- [ ] Materialized view contains accurate data

#### Related Stories
- All subsequent stories depend on this setup

---

### Story Card: FE-001 - React Project Initialization

**Priority:** High
**Story Points:** 3
**Sprint:** Sprint 1
**Status:** Todo
**Branch:** `setup/initial-project-structure`
**Assigned To:** react-architect

#### User Story
As a developer, I need a properly scaffolded React application so that I can begin building features with a solid foundation.

#### Description
Initialize a new React application with TypeScript, configure routing, set up folder structure, install necessary dependencies, and configure Supabase client.

#### Acceptance Criteria
- Given the need for a new React project
  When I run the initialization command
  Then a React + TypeScript project should be created with Vite

- Given the project dependencies
  When I install all required packages
  Then the project should have React Router, Supabase client, TanStack Query, and UI libraries installed

- Given the project structure requirements
  When I create the folder structure
  Then folders should exist for components, pages, hooks, contexts, services, types, and utils

- Given the Supabase credentials
  When I configure environment variables
  Then the Supabase client should connect successfully

- Given the routing requirements
  When I set up React Router
  Then routes should be configured for auth, home, profile, and post pages

#### Technical Requirements
- Use Vite for fast development and building
- TypeScript for type safety
- React Router v6 for routing
- Supabase JS client v2
- TanStack Query (React Query) for data fetching
- UI library: Choose between Tailwind CSS, Material-UI, or Chakra UI
- Form handling: React Hook Form
- State management: React Context API + hooks
- Testing: Vitest + React Testing Library

#### Design Decisions
- Vite chosen over Create React App for better performance
- TypeScript for catching errors early
- TanStack Query for server state management
- Context API sufficient for client state (avoid Redux complexity)

#### Implementation Notes
```bash
npm create vite@latest revere-network -- --template react-ts
cd revere-network
npm install @supabase/supabase-js
npm install react-router-dom
npm install @tanstack/react-query
npm install react-hook-form
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Folder structure:
```
src/
├── components/
│   ├── auth/
│   ├── posts/
│   ├── comments/
│   ├── notifications/
│   └── common/
├── pages/
├── hooks/
├── contexts/
├── services/
├── types/
├── utils/
└── App.tsx
```

#### Testing Checklist
- [ ] Project builds without errors
- [ ] All dependencies installed correctly
- [ ] TypeScript configuration is valid
- [ ] Supabase client connects successfully
- [ ] Routing works for all defined routes
- [ ] Development server starts without issues

#### Related Stories
- Depends on: DB-001
- Blocks: All feature stories

---

## Epic 2: Authentication & User Management

### Story Card: AUTH-001 - User Registration and Login

**Priority:** High
**Story Points:** 5
**Sprint:** Sprint 1
**Status:** Todo
**Branch:** `feature/auth-001-user-registration-login`
**Assigned To:** react-architect

#### User Story
As a new user, I want to register for an account and log in so that I can access the Revere Network platform.

#### Description
Implement user registration and login functionality using Supabase Auth with email/password authentication. Include form validation, error handling, and automatic profile creation.

#### Acceptance Criteria
- Given the registration form
  When I enter valid email, password, and username
  Then my account should be created and a profile record inserted automatically

- Given the login form
  When I enter correct credentials
  Then I should be authenticated and redirected to the home feed

- Given invalid credentials
  When I attempt to log in
  Then I should see an appropriate error message

- Given I am already logged in
  When I try to access login/register pages
  Then I should be redirected to the home feed

- Given the username field
  When I enter a username
  Then it should be validated for length (3-30 chars) and format (alphanumeric + underscore only)

- Given a username that's already taken (case-insensitive)
  When I try to register
  Then I should see an error indicating the username is unavailable

#### Technical Requirements
- Use Supabase Auth for authentication
- Implement email/password auth initially
- Create AuthContext for managing auth state
- Use React Hook Form for form handling
- Validate username availability before submission
- Implement protected route wrapper component
- Store auth session in Supabase (automatic)
- Handle auth state changes (login, logout, session refresh)

#### Design Decisions
- Email/password auth for MVP (can add OAuth later)
- Username chosen during registration (not changeable to prevent impersonation)
- Profile automatically created via database trigger
- AuthContext provides user data throughout app

#### Implementation Notes
Create files:
- `src/contexts/AuthContext.tsx` - Auth state management
- `src/components/auth/RegisterForm.tsx` - Registration form
- `src/components/auth/LoginForm.tsx` - Login form
- `src/pages/RegisterPage.tsx` - Registration page
- `src/pages/LoginPage.tsx` - Login page
- `src/components/common/ProtectedRoute.tsx` - Route wrapper
- `src/services/auth.service.ts` - Auth service functions
- `src/types/auth.types.ts` - Auth-related types

Key functions:
```typescript
// auth.service.ts
export const registerUser = async (email: string, password: string, username: string)
export const loginUser = async (email: string, password: string)
export const logoutUser = async ()
export const checkUsernameAvailability = async (username: string)
```

#### Testing Checklist
- [ ] Unit tests for auth service functions
- [ ] Integration tests for registration flow
- [ ] Integration tests for login flow
- [ ] Form validation works correctly
- [ ] Username availability check works
- [ ] Error messages display appropriately
- [ ] Protected routes redirect unauthorized users
- [ ] Session persists across page refreshes
- [ ] Logout clears session properly

#### Related Stories
- Depends on: FE-001, DB-001
- Blocks: All user-facing features

---

### Story Card: AUTH-002 - User Profile Setup and Editing

**Priority:** High
**Story Points:** 3
**Sprint:** Sprint 1
**Status:** Todo
**Branch:** `feature/auth-002-profile-management`
**Assigned To:** react-architect

#### User Story
As a registered user, I want to view and edit my profile information so that I can customize my presence on the platform.

#### Description
Create profile page showing user information (username, display name, bio, avatar) with the ability to edit display name, bio, and upload/change avatar image.

#### Acceptance Criteria
- Given I am logged in
  When I navigate to my profile page
  Then I should see my username, display name, bio, and avatar

- Given the profile edit form
  When I update my display name or bio
  Then the changes should be saved to the database

- Given the avatar upload feature
  When I select an image file
  Then it should be uploaded to Supabase Storage and my profile updated

- Given an uploaded avatar
  When I view my profile or posts
  Then the avatar should be displayed correctly

- Given I try to upload a file that's too large (>2MB)
  When I select the file
  Then I should see an error message

- Given the privacy toggle
  When I switch between public and private account
  Then my account privacy should be updated accordingly

#### Technical Requirements
- Use Supabase Storage for avatar uploads
- Limit avatar file size to 2MB
- Accept common image formats (JPEG, PNG, GIF, WebP)
- Resize/optimize images client-side before upload
- Update profile using Supabase client
- Show loading states during upload and save
- Implement optimistic updates for better UX

#### Design Decisions
- Username not editable after registration (prevents impersonation)
- Display name can be changed anytime
- Avatar stored in Supabase Storage bucket 'avatars'
- File path: `avatars/{user_id}/avatar.{ext}`
- Privacy setting visible on profile page

#### Implementation Notes
Create files:
- `src/pages/ProfilePage.tsx` - Profile view and edit
- `src/components/profile/ProfileHeader.tsx` - Profile display
- `src/components/profile/ProfileEditForm.tsx` - Edit form
- `src/components/profile/AvatarUpload.tsx` - Avatar upload component
- `src/services/profile.service.ts` - Profile CRUD operations
- `src/services/storage.service.ts` - Storage operations
- `src/types/profile.types.ts` - Profile types
- `src/hooks/useProfile.ts` - Profile data hook

Key functions:
```typescript
// profile.service.ts
export const getProfile = async (userId: string)
export const updateProfile = async (userId: string, updates: ProfileUpdate)

// storage.service.ts
export const uploadAvatar = async (userId: string, file: File)
export const getAvatarUrl = (userId: string)
export const deleteAvatar = async (userId: string)
```

#### Testing Checklist
- [ ] Profile page displays user data correctly
- [ ] Profile updates save successfully
- [ ] Avatar upload works with valid files
- [ ] Large files are rejected with error message
- [ ] Avatar displays after upload
- [ ] Privacy toggle updates database
- [ ] Optimistic updates provide good UX
- [ ] Error handling works for all operations

#### Related Stories
- Depends on: AUTH-001
- Related to: PRIV-001

---

## Epic 3: Post Management

### Story Card: POST-001 - Create and View Posts

**Priority:** High
**Story Points:** 5
**Sprint:** Sprint 2
**Status:** Todo
**Branch:** `feature/post-001-create-view-posts`
**Assigned To:** react-architect

#### User Story
As a user, I want to create text posts and view posts from other users so that I can share my thoughts and see what others are posting.

#### Description
Implement post creation functionality with a compose form, character counter, and display posts in a feed with author information, timestamps, and engagement metrics.

#### Acceptance Criteria
- Given I am on the home page
  When I type in the post composer
  Then I should see a character counter showing remaining characters (500 max)

- Given I have written a post
  When I click the post button
  Then the post should be created and appear at the top of my feed

- Given the home feed
  When I load the page
  Then I should see posts from users I follow in chronological order (newest first)

- Given a post in the feed
  When I view it
  Then I should see the author's avatar, username, display name, post content, timestamp, and engagement counts (likes, dislikes, comments)

- Given I have no posts in my feed
  When I load the home page
  Then I should see a helpful empty state message

- Given I scroll to the bottom of the feed
  When more posts are available
  Then the next page of posts should load automatically (infinite scroll)

#### Technical Requirements
- Use TanStack Query for data fetching and caching
- Implement infinite scroll using Intersection Observer API
- Character limit: 500 characters
- Real-time character counter
- Optimistic updates when creating posts
- Cache invalidation after post creation
- Proper loading and error states
- Responsive design for mobile and desktop

#### Design Decisions
- Text-only posts in this story (media added in POST-002)
- Infinite scroll preferred over pagination for better UX
- Optimistic updates make posting feel instant
- Feed shows posts from followed users + own posts
- Chronological order by default (popularity in separate story)

#### Implementation Notes
Create files:
- `src/components/posts/PostComposer.tsx` - Create post form
- `src/components/posts/PostCard.tsx` - Individual post display
- `src/components/posts/PostFeed.tsx` - Feed container with infinite scroll
- `src/pages/HomePage.tsx` - Main feed page
- `src/services/posts.service.ts` - Post CRUD operations
- `src/hooks/usePosts.ts` - Posts data fetching hook
- `src/hooks/useCreatePost.ts` - Post creation hook
- `src/types/post.types.ts` - Post-related types

Key functions:
```typescript
// posts.service.ts
export const createPost = async (content: string)
export const getFeed = async (page: number, limit: number)
export const getPost = async (postId: string)

// usePosts.ts (using TanStack Query)
export const useFeed = (enabled: boolean) => useInfiniteQuery(...)
export const usePost = (postId: string) => useQuery(...)

// useCreatePost.ts
export const useCreatePost = () => useMutation(...)
```

#### Testing Checklist
- [ ] Post composer validates character limit
- [ ] Character counter updates in real-time
- [ ] Posts created successfully
- [ ] Feed displays posts correctly
- [ ] Infinite scroll loads more posts
- [ ] Empty state shows when no posts available
- [ ] Loading states display during fetch
- [ ] Error states handle failures gracefully
- [ ] Optimistic updates work correctly
- [ ] Responsive design works on mobile

#### Related Stories
- Depends on: AUTH-001
- Blocks: POST-002, POST-003, POST-004

---

### Story Card: POST-002 - Add Media to Posts

**Priority:** High
**Story Points:** 5
**Sprint:** Sprint 2
**Status:** Todo
**Branch:** `feature/post-002-media-uploads`
**Assigned To:** react-architect

#### User Story
As a user, I want to attach images or videos to my posts so that I can share visual content with my followers.

#### Description
Extend post creation to support image and video uploads. Display media in posts with proper formatting and allow users to remove media before posting.

#### Acceptance Criteria
- Given the post composer
  When I click the media button
  Then I should be able to select image or video files

- Given I select media files
  When they are valid (under size limit, correct format)
  Then they should be uploaded and preview thumbnails shown

- Given I have attached media
  When I remove an attachment
  Then it should be removed from the post and deleted from storage

- Given I create a post with media
  When the post is published
  Then the media should display correctly in the feed

- Given a post with multiple images
  When I view it
  Then images should be displayed in a grid layout

- Given a post with video
  When I view it
  Then the video should be playable inline

- Given I try to upload a file that's too large
  When I select the file
  Then I should see an error message with the size limit

#### Technical Requirements
- Support images: JPEG, PNG, GIF, WebP (max 5MB each)
- Support videos: MP4, WebM (max 50MB each)
- Maximum 4 media attachments per post
- Upload to Supabase Storage bucket 'post-media'
- Client-side image compression before upload
- Generate thumbnails for videos
- Progress indicator during upload
- Lazy loading for media in feed

#### Design Decisions
- Up to 4 media items per post (like Twitter)
- Media stored in Supabase Storage with unique file names
- File path: `post-media/{user_id}/{post_id}/{filename}`
- Images displayed in responsive grid (1 image: full width, 2: side-by-side, 3+: grid)
- Videos play inline without sound by default

#### Implementation Notes
Update files:
- `src/components/posts/PostComposer.tsx` - Add media picker
- `src/components/posts/MediaUpload.tsx` - Media upload component
- `src/components/posts/MediaPreview.tsx` - Preview before posting
- `src/components/posts/PostMedia.tsx` - Display media in posts
- `src/services/storage.service.ts` - Add media upload functions
- Update `src/types/post.types.ts` - Add media fields

Libraries to add:
- `browser-image-compression` for client-side compression
- `react-lazy-load-image-component` for lazy loading

Key functions:
```typescript
// storage.service.ts
export const uploadPostMedia = async (userId: string, postId: string, files: File[])
export const deletePostMedia = async (mediaUrls: string[])
export const compressImage = async (file: File)
```

#### Testing Checklist
- [ ] Media picker opens and accepts correct file types
- [ ] File size validation works
- [ ] Upload progress indicator displays
- [ ] Preview thumbnails show correctly
- [ ] Media can be removed before posting
- [ ] Posts with media save correctly
- [ ] Media displays properly in feed
- [ ] Image grid layout works for 1-4 images
- [ ] Videos play inline
- [ ] Lazy loading improves performance
- [ ] Failed uploads show error messages

#### Related Stories
- Depends on: POST-001
- Blocks: POST-003 (edit will need to handle media)

---

### Story Card: POST-003 - Edit Posts

**Priority:** Medium
**Story Points:** 3
**Sprint:** Sprint 2
**Status:** Todo
**Branch:** `feature/post-003-edit-posts`
**Assigned To:** react-architect

#### User Story
As a user, I want to edit my own posts so that I can fix typos or update information while maintaining transparency about edits.

#### Description
Allow users to edit their own posts with an edit button, edit form, and display edit history. Show "edited" indicator on edited posts and allow viewing edit history.

#### Acceptance Criteria
- Given I am viewing my own post
  When I click the edit button
  Then an edit form should appear with the current post content and media

- Given the edit form
  When I make changes and save
  Then the post should be updated and marked as edited

- Given an edited post
  When I view it in the feed
  Then it should show an "edited" indicator with the edit timestamp

- Given the edit history feature
  When I click the "edited" indicator
  Then I should see the full edit history with timestamps

- Given I am viewing someone else's post
  When I look for the edit button
  Then it should not be visible

- Given I edit a post with media
  When I add, remove, or change media
  Then the media should be updated and old media deleted from storage

#### Technical Requirements
- Edit button only visible on user's own posts
- Edit form pre-populated with current content
- Database trigger automatically saves edit history
- Display edit indicator if `edited_at` is not null
- Edit history modal/dropdown showing all versions
- Maintain character limits during editing
- Handle media changes (add, remove, replace)
- Delete orphaned media from storage

#### Design Decisions
- Edit history stored automatically via database trigger
- Show edit timestamp in relative time (e.g., "edited 2h ago")
- Edit history accessible to anyone who can view the post
- No limit on number of edits
- Original post preserved in edit history

#### Implementation Notes
Update files:
- `src/components/posts/PostCard.tsx` - Add edit button and indicator
- `src/components/posts/PostEditForm.tsx` - Edit form component
- `src/components/posts/EditHistory.tsx` - Display edit history
- `src/services/posts.service.ts` - Add update function
- `src/hooks/useUpdatePost.ts` - Post update hook
- `src/hooks/useEditHistory.ts` - Edit history fetching

Key functions:
```typescript
// posts.service.ts
export const updatePost = async (postId: string, updates: PostUpdate)
export const getEditHistory = async (postId: string)

// useUpdatePost.ts
export const useUpdatePost = () => useMutation(...)
export const useEditHistory = (postId: string) => useQuery(...)
```

#### Testing Checklist
- [ ] Edit button only appears on own posts
- [ ] Edit form loads with current content
- [ ] Post updates save correctly
- [ ] Edit history is recorded
- [ ] Edited indicator displays correctly
- [ ] Edit history modal shows all versions
- [ ] Media changes handled properly
- [ ] Orphaned media deleted from storage
- [ ] Character limits enforced during edit
- [ ] Optimistic updates work

#### Related Stories
- Depends on: POST-001, POST-002
- Related to: HIST-001

---

### Story Card: POST-004 - Delete Posts

**Priority:** Medium
**Story Points:** 2
**Sprint:** Sprint 2
**Status:** Todo
**Branch:** `feature/post-004-delete-posts`
**Assigned To:** react-architect

#### User Story
As a user, I want to delete my own posts so that I can remove content I no longer want public.

#### Description
Implement post deletion with confirmation dialog and soft delete in database. Remove post from feeds and mark as deleted without actually removing from database.

#### Acceptance Criteria
- Given I am viewing my own post
  When I click the delete button
  Then a confirmation dialog should appear

- Given the delete confirmation dialog
  When I confirm deletion
  Then the post should be soft-deleted and removed from all feeds

- Given a deleted post
  When I try to access it directly
  Then I should see a "Post not found" message

- Given I am viewing someone else's post
  When I look for the delete button
  Then it should not be visible

- Given a post with media
  When I delete it
  Then the media files should remain in storage (for potential recovery)

#### Technical Requirements
- Soft delete: set `is_deleted = TRUE` in database
- Delete button only visible on user's own posts
- Confirmation dialog prevents accidental deletion
- Remove from cache after deletion
- Update feed optimistically
- RLS policies prevent viewing deleted posts
- Keep media in storage for recovery window

#### Design Decisions
- Soft delete for data retention and potential undo feature
- Media kept in storage for 30 days before cleanup (admin job)
- No immediate hard delete option in UI
- Confirmation required to prevent accidental deletion

#### Implementation Notes
Update files:
- `src/components/posts/PostCard.tsx` - Add delete button
- `src/components/common/ConfirmDialog.tsx` - Reusable confirmation dialog
- `src/services/posts.service.ts` - Add delete function
- `src/hooks/useDeletePost.ts` - Post deletion hook

Key functions:
```typescript
// posts.service.ts
export const deletePost = async (postId: string)

// useDeletePost.ts
export const useDeletePost = () => useMutation(...)
```

#### Testing Checklist
- [ ] Delete button only appears on own posts
- [ ] Confirmation dialog appears on delete
- [ ] Post removed from feed after deletion
- [ ] Deleted posts inaccessible via direct link
- [ ] Cache invalidation works correctly
- [ ] Optimistic updates work
- [ ] Media remains in storage
- [ ] Related comments marked as deleted (cascade)

#### Related Stories
- Depends on: POST-001

---

## Epic 4: Comments

### Story Card: COMM-001 - Create and View Comments

**Priority:** High
**Story Points:** 5
**Sprint:** Sprint 3
**Status:** Todo
**Branch:** `feature/comm-001-comments`
**Assigned To:** react-architect

#### User Story
As a user, I want to comment on posts so that I can engage in conversations and provide feedback.

#### Description
Implement comment functionality allowing users to add comments to posts and view all comments on a post. Display comments with author information and timestamps.

#### Acceptance Criteria
- Given a post in the feed
  When I click the comment button/icon
  Then I should see the post detail view with comments section

- Given the comment form
  When I type a comment and submit
  Then the comment should be added and appear immediately

- Given a post with comments
  When I view the post
  Then I should see all comments in chronological order (oldest first)

- Given a comment
  When I view it
  Then I should see the author's avatar, username, comment text, and timestamp

- Given I write a comment
  When I exceed 500 characters
  Then the submit button should be disabled and I should see a character count

- Given I am not logged in
  When I try to comment
  Then I should be prompted to log in

#### Technical Requirements
- Comment form below post or in post detail page
- Character limit: 500 characters
- Real-time character counter
- Optimistic updates for comments
- Load comments on demand (not in feed initially)
- Comment count displayed on post card
- Nested component structure (PostCard -> CommentList -> CommentItem)

#### Design Decisions
- Comments shown in chronological order (oldest first) for conversation flow
- Comments loaded when post is expanded/clicked
- Character limit matches posts (500 chars)
- No nested replies in MVP (can add threading later)
- Comment count badge on post cards

#### Implementation Notes
Create files:
- `src/components/comments/CommentForm.tsx` - Create comment
- `src/components/comments/CommentList.tsx` - List of comments
- `src/components/comments/CommentItem.tsx` - Individual comment
- `src/pages/PostDetailPage.tsx` - Full post view with comments
- `src/services/comments.service.ts` - Comment CRUD operations
- `src/hooks/useComments.ts` - Comments fetching hook
- `src/hooks/useCreateComment.ts` - Comment creation hook
- `src/types/comment.types.ts` - Comment types

Key functions:
```typescript
// comments.service.ts
export const createComment = async (postId: string, content: string)
export const getComments = async (postId: string)
export const getComment = async (commentId: string)

// useComments.ts
export const useComments = (postId: string) => useQuery(...)

// useCreateComment.ts
export const useCreateComment = () => useMutation(...)
```

#### Testing Checklist
- [ ] Comment form validates character limit
- [ ] Comments created successfully
- [ ] Comments display with correct author info
- [ ] Comment count updates on post cards
- [ ] Comments load when post is expanded
- [ ] Chronological ordering works
- [ ] Optimistic updates work
- [ ] Loading states display
- [ ] Error handling works
- [ ] Non-authenticated users see login prompt

#### Related Stories
- Depends on: POST-001
- Blocks: COMM-002, REACT-002

---

### Story Card: COMM-002 - Edit and Delete Comments

**Priority:** Low
**Story Points:** 2
**Sprint:** Sprint 3
**Status:** Todo
**Branch:** `feature/comm-002-edit-delete-comments`
**Assigned To:** react-architect

#### User Story
As a user, I want to edit or delete my own comments so that I can correct mistakes or remove comments I no longer want public.

#### Description
Add edit and delete functionality for comments similar to posts. Users can only edit/delete their own comments.

#### Acceptance Criteria
- Given I am viewing my own comment
  When I click the edit button
  Then an inline edit form should appear

- Given the comment edit form
  When I make changes and save
  Then the comment should be updated

- Given I am viewing my own comment
  When I click the delete button and confirm
  Then the comment should be soft-deleted and removed from view

- Given I am viewing someone else's comment
  When I look for edit/delete buttons
  Then they should not be visible

#### Technical Requirements
- Inline editing for better UX
- Soft delete for comments
- Character limit enforcement during editing
- Confirmation dialog for deletion
- Optimistic updates

#### Design Decisions
- Inline editing preferred over modal
- Soft delete maintains data integrity
- No edit history for comments (simpler than posts)

#### Implementation Notes
Update files:
- `src/components/comments/CommentItem.tsx` - Add edit/delete UI
- `src/components/comments/CommentEditForm.tsx` - Inline edit form
- `src/services/comments.service.ts` - Add update and delete functions
- `src/hooks/useUpdateComment.ts` - Comment update hook
- `src/hooks/useDeleteComment.ts` - Comment deletion hook

Key functions:
```typescript
// comments.service.ts
export const updateComment = async (commentId: string, content: string)
export const deleteComment = async (commentId: string)
```

#### Testing Checklist
- [ ] Edit button only appears on own comments
- [ ] Inline edit form works
- [ ] Comment updates save correctly
- [ ] Delete confirmation appears
- [ ] Comments removed after deletion
- [ ] Optimistic updates work

#### Related Stories
- Depends on: COMM-001

---

## Epic 5: Reactions (Likes/Dislikes)

### Story Card: REACT-001 - Like/Dislike Posts

**Priority:** High
**Story Points:** 3
**Sprint:** Sprint 3
**Status:** Todo
**Branch:** `feature/react-001-post-reactions`
**Assigned To:** react-architect

#### User Story
As a user, I want to like or dislike posts so that I can express my opinion and engage with content.

#### Description
Implement like/dislike buttons on posts with visual feedback, count displays, and the ability to change or remove reactions.

#### Acceptance Criteria
- Given a post in the feed
  When I click the like button
  Then the post should be liked and the like count should increase

- Given a post I have liked
  When I click the dislike button
  Then my reaction should change from like to dislike

- Given a post I have reacted to
  When I click the same reaction button again
  Then my reaction should be removed

- Given a post
  When I view it
  Then I should see the current like and dislike counts

- Given I am not logged in
  When I try to react to a post
  Then I should be prompted to log in

- Given my reactions
  When I view posts
  Then posts I've liked should show a filled like icon and posts I've disliked should show a filled dislike icon

#### Technical Requirements
- Unique constraint prevents duplicate reactions
- Update reaction (like to dislike or vice versa)
- Remove reaction (unlike/undislike)
- Optimistic updates for instant feedback
- Display current user's reaction state
- Real-time count updates
- Debounce rapid clicking

#### Design Decisions
- One reaction per user per post (enforced by DB constraint)
- Clicking same button removes reaction
- Clicking opposite button changes reaction
- Separate like and dislike counts displayed
- Visual indicator for user's current reaction

#### Implementation Notes
Create files:
- `src/components/posts/PostReactions.tsx` - Reaction buttons component
- `src/services/reactions.service.ts` - Reaction operations
- `src/hooks/usePostReaction.ts` - Reaction mutation hook
- `src/types/reaction.types.ts` - Reaction types

Update files:
- `src/components/posts/PostCard.tsx` - Add reactions component

Key functions:
```typescript
// reactions.service.ts
export const addReaction = async (postId: string, type: 'like' | 'dislike')
export const updateReaction = async (postId: string, type: 'like' | 'dislike')
export const removeReaction = async (postId: string)
export const getUserReaction = async (postId: string)

// usePostReaction.ts
export const usePostReaction = (postId: string) => useMutation(...)
```

#### Testing Checklist
- [ ] Like button adds like reaction
- [ ] Dislike button adds dislike reaction
- [ ] Changing reaction updates correctly
- [ ] Removing reaction works
- [ ] Counts display accurately
- [ ] Visual feedback shows user's reaction
- [ ] Optimistic updates work
- [ ] Debouncing prevents spam
- [ ] Non-authenticated users prompted to login

#### Related Stories
- Depends on: POST-001
- Blocks: FEED-002 (popularity feed needs reaction counts)

---

### Story Card: REACT-002 - Like/Dislike Comments

**Priority:** Medium
**Story Points:** 2
**Sprint:** Sprint 3
**Status:** Todo
**Branch:** `feature/react-002-comment-reactions`
**Assigned To:** react-architect

#### User Story
As a user, I want to like or dislike comments so that I can express agreement or disagreement with specific comments.

#### Description
Extend reaction functionality to comments, similar to post reactions.

#### Acceptance Criteria
- Given a comment
  When I click the like or dislike button
  Then the comment should be reacted to and the count should update

- Given a comment I have reacted to
  When I click the opposite reaction button
  Then my reaction should change

- Given a comment I have reacted to
  When I click the same button again
  Then my reaction should be removed

- Given comments in a thread
  When I view them
  Then I should see like/dislike counts and my current reactions

#### Technical Requirements
- Same logic as post reactions
- Separate table: `comment_reactions`
- Optimistic updates
- Visual indicators for user's reactions

#### Design Decisions
- Consistent behavior with post reactions
- Smaller buttons for comment reactions (space-saving)

#### Implementation Notes
Create files:
- `src/components/comments/CommentReactions.tsx` - Comment reaction buttons

Update files:
- `src/components/comments/CommentItem.tsx` - Add reactions component
- `src/services/reactions.service.ts` - Add comment reaction functions
- `src/hooks/useCommentReaction.ts` - Comment reaction hook

Key functions:
```typescript
// reactions.service.ts
export const addCommentReaction = async (commentId: string, type: 'like' | 'dislike')
export const updateCommentReaction = async (commentId: string, type: 'like' | 'dislike')
export const removeCommentReaction = async (commentId: string)
```

#### Testing Checklist
- [ ] Comment reactions work same as post reactions
- [ ] Counts update correctly
- [ ] Visual feedback works
- [ ] Optimistic updates function properly

#### Related Stories
- Depends on: COMM-001, REACT-001

---

## Epic 6: Follow System

### Story Card: FOLLOW-001 - Follow/Unfollow Users

**Priority:** High
**Story Points:** 3
**Sprint:** Sprint 4
**Status:** Todo
**Branch:** `feature/follow-001-follow-unfollow`
**Assigned To:** react-architect

#### User Story
As a user, I want to follow other users so that I can see their posts in my feed.

#### Description
Implement follow/unfollow functionality with automatic handling of public and private accounts.

#### Acceptance Criteria
- Given a user profile I'm viewing
  When I click the follow button
  Then I should follow that user (if public) or send a follow request (if private)

- Given a user I am following
  When I click the unfollow button
  Then I should unfollow that user

- Given a public account
  When I follow them
  Then their posts should immediately appear in my feed

- Given a private account
  When I send a follow request
  Then the request should be pending until they approve

- Given my own profile
  When I view it
  Then I should not see a follow button

- Given a user's profile
  When I view it
  Then I should see their follower count and following count

#### Technical Requirements
- Follow button state: "Follow", "Following", "Requested"
- Database trigger sets follow status based on account privacy
- RLS policies filter private account posts from non-followers
- Optimistic updates for public accounts
- Display follower/following counts
- Prevent self-follows (DB constraint)

#### Design Decisions
- Auto-approve follows for public accounts
- Pending status for private accounts
- Trigger handles status automatically based on target account privacy
- Unfollow is instant (no confirmation needed)

#### Implementation Notes
Create files:
- `src/components/profile/FollowButton.tsx` - Follow/unfollow button
- `src/components/profile/FollowStats.tsx` - Follower/following counts
- `src/services/follows.service.ts` - Follow operations
- `src/hooks/useFollow.ts` - Follow mutation hook
- `src/hooks/useFollowStats.ts` - Stats fetching hook
- `src/types/follow.types.ts` - Follow types

Update files:
- `src/pages/ProfilePage.tsx` - Add follow button and stats

Key functions:
```typescript
// follows.service.ts
export const followUser = async (userId: string)
export const unfollowUser = async (userId: string)
export const getFollowStatus = async (userId: string)
export const getFollowers = async (userId: string)
export const getFollowing = async (userId: string)
export const getFollowStats = async (userId: string)

// useFollow.ts
export const useFollow = () => useMutation(...)
export const useUnfollow = () => useMutation(...)
```

#### Testing Checklist
- [ ] Follow button works on public accounts
- [ ] Follow requests sent to private accounts
- [ ] Unfollow removes relationship
- [ ] Follow status displays correctly
- [ ] Follower/following counts accurate
- [ ] Cannot follow self
- [ ] Optimistic updates work
- [ ] Feed updates after following

#### Related Stories
- Depends on: AUTH-002
- Blocks: FOLLOW-002, FEED-001
- Related to: PRIV-001

---

### Story Card: FOLLOW-002 - Manage Follow Requests (Private Accounts)

**Priority:** Medium
**Story Points:** 3
**Sprint:** Sprint 4
**Status:** Todo
**Branch:** `feature/follow-002-follow-requests`
**Assigned To:** react-architect

#### User Story
As a user with a private account, I want to review and approve/deny follow requests so that I can control who sees my content.

#### Description
Create an interface for users with private accounts to view pending follow requests and approve or deny them.

#### Acceptance Criteria
- Given I have a private account
  When I navigate to my follow requests page
  Then I should see all pending follow requests

- Given a pending follow request
  When I click approve
  Then the follow status should change to accepted and the user should see my posts

- Given a pending follow request
  When I click deny
  Then the follow relationship should be deleted

- Given I have follow requests
  When a new request arrives
  Then I should see a badge count on the requests icon/link

- Given I have no follow requests
  When I view the requests page
  Then I should see an empty state message

#### Technical Requirements
- Page/modal showing pending follow requests
- Approve updates follow status to 'accepted'
- Deny deletes the follow relationship
- Real-time or periodic polling for new requests
- Badge count for pending requests
- Optimistic updates when approving/denying

#### Design Decisions
- Separate page/tab for follow requests
- Approve/deny actions with immediate feedback
- Badge notification for pending count
- Denied requests completely removed (not tracked)

#### Implementation Notes
Create files:
- `src/pages/FollowRequestsPage.tsx` - Follow requests page
- `src/components/follows/FollowRequestItem.tsx` - Individual request
- `src/components/follows/FollowRequestsList.tsx` - List of requests
- `src/hooks/useFollowRequests.ts` - Fetch pending requests
- `src/hooks/useApproveRequest.ts` - Approve mutation
- `src/hooks/useDenyRequest.ts` - Deny mutation

Update files:
- `src/services/follows.service.ts` - Add approve/deny functions

Key functions:
```typescript
// follows.service.ts
export const getPendingRequests = async ()
export const approveFollowRequest = async (followId: string)
export const denyFollowRequest = async (followId: string)

// useFollowRequests.ts
export const useFollowRequests = () => useQuery(...)
export const useApproveRequest = () => useMutation(...)
export const useDenyRequest = () => useMutation(...)
```

#### Testing Checklist
- [ ] Pending requests display correctly
- [ ] Approve updates status and grants access
- [ ] Deny removes follow relationship
- [ ] Badge count shows pending count
- [ ] Empty state displays when no requests
- [ ] Optimistic updates work
- [ ] Feed updates after approving

#### Related Stories
- Depends on: FOLLOW-001, PRIV-001

---

## Epic 7: Feed Views

### Story Card: FEED-001 - Chronological Feed

**Priority:** High
**Story Points:** 3
**Sprint:** Sprint 2
**Status:** Todo
**Branch:** `feature/feed-001-chronological-feed`
**Assigned To:** react-architect

#### User Story
As a user, I want to view posts in chronological order so that I can see the latest content from people I follow.

#### Description
Implement the main feed showing posts from followed users in chronological order (newest first) with infinite scroll.

#### Acceptance Criteria
- Given I am on the home page
  When the feed loads
  Then I should see posts from users I follow plus my own posts, newest first

- Given I scroll to the bottom of the feed
  When more posts are available
  Then the next page should load automatically

- Given I have no follows and no posts
  When I view the home feed
  Then I should see a helpful empty state encouraging me to follow users or create posts

- Given new posts are created by users I follow
  When I refresh the page
  Then the new posts should appear at the top

#### Technical Requirements
- Use database function `get_chronological_feed()`
- Infinite scroll with TanStack Query
- Efficient pagination (20 posts per page)
- Include user's own posts in feed
- Filter out posts from unfollowed users
- Respect private account settings
- Cache feed data for performance

#### Design Decisions
- Chronological order: newest to oldest
- Include own posts for consistency
- 20 posts per page balances performance and UX
- Infinite scroll preferred over pagination buttons

#### Implementation Notes
This is partially implemented in POST-001 but needs refinement.

Update files:
- `src/services/posts.service.ts` - Use chronological feed function
- `src/hooks/useFeed.ts` - Feed fetching with proper ordering
- `src/pages/HomePage.tsx` - Ensure chronological ordering

Key functions:
```typescript
// posts.service.ts
export const getChronologicalFeed = async (page: number, limit: number)
```

#### Testing Checklist
- [ ] Feed shows posts in chronological order
- [ ] Own posts included in feed
- [ ] Posts from followed users appear
- [ ] Posts from unfollowed users don't appear
- [ ] Private account posts filtered correctly
- [ ] Infinite scroll works
- [ ] Empty state displays when appropriate
- [ ] Performance is acceptable with large feeds

#### Related Stories
- Depends on: POST-001, FOLLOW-001
- Related to: FEED-002

---

### Story Card: FEED-002 - Popularity Feed

**Priority:** Medium
**Story Points:** 3
**Sprint:** Sprint 4
**Status:** Todo
**Branch:** `feature/feed-002-popularity-feed`
**Assigned To:** react-architect

#### User Story
As a user, I want to view posts sorted by popularity so that I can discover trending content and highly-engaged posts.

#### Description
Add a popularity feed view showing posts sorted by engagement metrics (popularity score, most liked, or most disliked).

#### Acceptance Criteria
- Given I am on the home page
  When I switch to the "Popular" tab
  Then I should see posts sorted by popularity score (likes - dislikes)

- Given the popularity feed
  When I select "Most Liked" filter
  Then posts should be sorted by like count descending

- Given the popularity feed
  When I select "Most Disliked" filter
  Then posts should be sorted by dislike count descending

- Given the popularity feed
  When I scroll to the bottom
  Then more popular posts should load

- Given I switch between chronological and popular feeds
  When I navigate
  Then the active tab should be visually indicated

#### Technical Requirements
- Use database function `get_popular_feed()`
- Tab/toggle to switch between chronological and popular
- Filter dropdown: "Popular", "Most Liked", "Most Disliked"
- Uses materialized view for performance
- Infinite scroll on popularity feed
- Separate cache for each feed type

#### Design Decisions
- Three popularity sorting options
- Popularity score = likes - dislikes
- Uses materialized view refreshed every 5 minutes
- Separate feed state from chronological feed
- Tab interface for switching feeds

#### Implementation Notes
Create files:
- `src/components/feed/FeedTabs.tsx` - Tab switcher
- `src/components/feed/PopularityFilter.tsx` - Filter dropdown
- `src/hooks/usePopularFeed.ts` - Popular feed fetching

Update files:
- `src/pages/HomePage.tsx` - Add tabs and popular feed
- `src/services/posts.service.ts` - Add popular feed function

Key functions:
```typescript
// posts.service.ts
export const getPopularFeed = async (
  sortBy: 'popularity' | 'likes' | 'dislikes',
  page: number,
  limit: number
)

// usePopularFeed.ts
export const usePopularFeed = (sortBy: string) => useInfiniteQuery(...)
```

#### Testing Checklist
- [ ] Popular tab displays posts by popularity score
- [ ] Most Liked filter sorts by likes
- [ ] Most Disliked filter sorts by dislikes
- [ ] Tab switching works smoothly
- [ ] Infinite scroll works on popular feed
- [ ] Active tab visually indicated
- [ ] Feed cache separate from chronological
- [ ] Performance acceptable

#### Related Stories
- Depends on: POST-001, REACT-001
- Related to: FEED-001

---

## Epic 8: Notifications

### Story Card: NOTIF-001 - Notification System

**Priority:** Medium
**Story Points:** 5
**Sprint:** Sprint 5
**Status:** Todo
**Branch:** `feature/notif-001-notification-system`
**Assigned To:** react-architect

#### User Story
As a user, I want to receive notifications when others interact with my content so that I can stay engaged with my community.

#### Description
Implement notification system showing likes, dislikes, comments, follows, and follow requests with real-time or periodic updates.

#### Acceptance Criteria
- Given someone likes my post
  When I check notifications
  Then I should see a notification with the actor's name and post

- Given someone comments on my post
  When I check notifications
  Then I should see a notification with the actor's name, comment preview, and post

- Given someone follows me
  When I check notifications
  Then I should see a follow notification

- Given someone sends me a follow request (private account)
  When I check notifications
  Then I should see a follow request notification

- Given I have unread notifications
  When I view the notification icon
  Then I should see a badge with the unread count

- Given I open the notifications panel
  When I view notifications
  Then they should be marked as read

#### Technical Requirements
- Notifications created via database triggers (already set up in schema)
- Notification types: follow, follow_request, post_like, post_dislike, comment, comment_like, comment_dislike
- Real-time updates using Supabase Realtime subscriptions
- Mark as read functionality
- Group similar notifications (e.g., "John and 5 others liked your post")
- Clear all notifications option
- Link to source post/comment

#### Design Decisions
- Notifications auto-created by triggers
- Real-time updates via Supabase Realtime
- Badge shows unread count
- Dropdown/panel interface
- Notifications marked read on view
- Keep notifications indefinitely (user can delete)

#### Implementation Notes
Create files:
- `src/components/notifications/NotificationBell.tsx` - Bell icon with badge
- `src/components/notifications/NotificationPanel.tsx` - Dropdown panel
- `src/components/notifications/NotificationItem.tsx` - Individual notification
- `src/services/notifications.service.ts` - Notification operations
- `src/hooks/useNotifications.ts` - Fetch notifications
- `src/hooks/useNotificationSubscription.ts` - Realtime subscription
- `src/types/notification.types.ts` - Notification types

Key functions:
```typescript
// notifications.service.ts
export const getNotifications = async (limit: number, offset: number)
export const getUnreadCount = async ()
export const markAsRead = async (notificationId: string)
export const markAllAsRead = async ()
export const deleteNotification = async (notificationId: string)

// useNotificationSubscription.ts
export const useNotificationSubscription = () => {
  // Subscribe to notifications table for current user
  // Update query cache on new notifications
}
```

#### Testing Checklist
- [ ] Notifications created automatically on interactions
- [ ] Notification types display correctly
- [ ] Unread badge shows correct count
- [ ] Real-time updates work
- [ ] Marking as read updates state
- [ ] Mark all as read works
- [ ] Delete notification works
- [ ] Links to posts/comments work
- [ ] Notification panel scrollable

#### Related Stories
- Depends on: POST-001, COMM-001, REACT-001, FOLLOW-001
- Blocks: None

---

## Epic 9: Privacy Features

### Story Card: PRIV-001 - Private Account Settings

**Priority:** Medium
**Story Points:** 3
**Sprint:** Sprint 4
**Status:** Todo
**Branch:** `feature/priv-001-private-accounts`
**Assigned To:** react-architect

#### User Story
As a user, I want to make my account private so that only approved followers can see my posts.

#### Description
Implement private account toggle in profile settings and enforce privacy throughout the application.

#### Acceptance Criteria
- Given my profile settings
  When I toggle "Private Account" on
  Then my account should become private and new follows should require approval

- Given my account is private
  When someone tries to follow me
  Then they should send a follow request instead of immediately following

- Given my account is private
  When a non-follower views my profile
  Then they should not see my posts, only my profile information and a "Follow" button

- Given I switch from private to public
  When I save settings
  Then all pending follow requests should be automatically approved

- Given my account is public
  When someone follows me
  Then they should immediately see my posts in their feed

#### Technical Requirements
- Toggle in profile settings page
- Database trigger handles follow status based on privacy setting
- RLS policies enforce post visibility
- Approve all pending requests when switching to public
- Display lock icon on private profiles
- Filter posts in feed based on follow status and privacy

#### Design Decisions
- Privacy toggle in profile settings
- Switching to public auto-approves pending requests
- Private profiles show limited information to non-followers
- Lock icon indicates private account

#### Implementation Notes
Create files:
- `src/components/profile/PrivacySettings.tsx` - Privacy toggle

Update files:
- `src/pages/ProfilePage.tsx` - Show privacy indicator
- `src/components/profile/ProfileEditForm.tsx` - Add privacy toggle
- `src/services/profile.service.ts` - Add privacy update function
- `src/services/follows.service.ts` - Add auto-approve function

Key functions:
```typescript
// profile.service.ts
export const updatePrivacySetting = async (userId: string, isPrivate: boolean)

// follows.service.ts
export const approveAllPendingRequests = async (userId: string)
```

#### Testing Checklist
- [ ] Privacy toggle works
- [ ] Private accounts require follow approval
- [ ] Non-followers can't see private posts
- [ ] Lock icon displays on private profiles
- [ ] Switching to public auto-approves pending requests
- [ ] RLS policies enforce privacy correctly
- [ ] Feed respects privacy settings

#### Related Stories
- Depends on: AUTH-002, FOLLOW-001
- Related to: FOLLOW-002

---

## Epic 10: Edit History

### Story Card: HIST-001 - Post Edit History Viewer

**Priority:** Low
**Story Points:** 2
**Sprint:** Sprint 5
**Status:** Todo
**Branch:** `feature/hist-001-edit-history`
**Assigned To:** react-architect

#### User Story
As a user, I want to view the edit history of posts so that I can see what changes were made over time.

#### Description
Create an interface to view post edit history with timestamps and previous versions of content.

#### Acceptance Criteria
- Given an edited post
  When I click the "edited" indicator
  Then I should see a modal/panel showing edit history

- Given the edit history view
  When I view it
  Then I should see all previous versions with timestamps and content

- Given a post edited multiple times
  When I view edit history
  Then versions should be in reverse chronological order (newest first)

- Given a post with media edits
  When I view edit history
  Then I should see previous media versions

- Given a post that was never edited
  When I look for the edit history
  Then the edited indicator should not be present

#### Technical Requirements
- Fetch from `post_edit_history` table
- Modal or expandable panel for edit history
- Display timestamp for each edit
- Show content and media for each version
- Reverse chronological order (newest edit first)

#### Design Decisions
- Modal interface for edit history
- Edit history accessible to anyone who can view the post
- Show full content of each version (not diff view)
- Edit history maintained indefinitely

#### Implementation Notes
Create files:
- `src/components/posts/EditHistoryModal.tsx` - Edit history modal
- `src/components/posts/EditHistoryItem.tsx` - Individual version

Update files:
- `src/components/posts/PostCard.tsx` - Add click handler for edited indicator
- `src/services/posts.service.ts` - Already has getEditHistory function

Key functions:
```typescript
// posts.service.ts (already defined in schema)
export const getEditHistory = async (postId: string)

// useEditHistory.ts (created in POST-003)
export const useEditHistory = (postId: string) => useQuery(...)
```

#### Testing Checklist
- [ ] Edit history modal opens on click
- [ ] All edit versions display correctly
- [ ] Timestamps formatted properly
- [ ] Media versions shown in history
- [ ] Reverse chronological ordering
- [ ] Modal closes properly
- [ ] Edit indicator only on edited posts

#### Related Stories
- Depends on: POST-003
- Related to: None

---

## Epic 11: User Discovery & Search

### Story Card: SEARCH-001 - User Search

**Priority:** Medium
**Story Points:** 3
**Sprint:** Sprint 5
**Status:** Todo
**Branch:** `feature/search-001-user-search`
**Assigned To:** react-architect

#### User Story
As a user, I want to search for other users by username or display name so that I can find and follow people.

#### Description
Implement user search functionality with autocomplete and search results page.

#### Acceptance Criteria
- Given the search bar
  When I type a username or display name
  Then I should see autocomplete suggestions

- Given autocomplete suggestions
  When I click a suggestion
  Then I should navigate to that user's profile

- Given the search results page
  When I search for users
  Then I should see matching profiles with avatar, username, and display name

- Given I am searching
  When I type fewer than 2 characters
  Then no search should be triggered

- Given search results
  When results are returned
  Then they should be ordered by relevance (exact match first, then partial matches)

#### Technical Requirements
- Search by username (case-insensitive) and display name
- Autocomplete with debouncing (300ms)
- PostgreSQL full-text search or ILIKE queries
- Limit autocomplete to 5 results
- Search results page with pagination
- Link to profile from search results

#### Design Decisions
- Debounced autocomplete for better performance
- Minimum 2 characters to search
- Case-insensitive search
- Simple ILIKE query for MVP (can upgrade to full-text search later)

#### Implementation Notes
Create files:
- `src/components/search/SearchBar.tsx` - Search input with autocomplete
- `src/components/search/SearchResults.tsx` - Search results page
- `src/components/search/UserSearchResult.tsx` - Individual result
- `src/services/search.service.ts` - Search operations
- `src/hooks/useUserSearch.ts` - Search query hook
- `src/types/search.types.ts` - Search types

Key functions:
```typescript
// search.service.ts
export const searchUsers = async (query: string, limit: number)

// useUserSearch.ts
export const useUserSearch = (query: string) => useQuery(...)
```

Database query:
```sql
-- Add to posts.service.ts or create search.service.ts
SELECT id, username, display_name, avatar_url
FROM profiles
WHERE username_lower LIKE LOWER($1 || '%')
   OR LOWER(display_name) LIKE LOWER('%' || $1 || '%')
ORDER BY
  CASE
    WHEN username_lower = LOWER($1) THEN 1
    WHEN username_lower LIKE LOWER($1 || '%') THEN 2
    ELSE 3
  END
LIMIT $2;
```

#### Testing Checklist
- [ ] Search input debounced
- [ ] Autocomplete suggestions appear
- [ ] Click suggestion navigates to profile
- [ ] Search results page displays matches
- [ ] Ordering by relevance works
- [ ] Minimum character check works
- [ ] Case-insensitive search works
- [ ] Performance acceptable

#### Related Stories
- Depends on: AUTH-002
- Related to: None

---

## Epic 12: Polish & Optimization

### Story Card: POLISH-001 - Responsive Design & Mobile Optimization

**Priority:** High
**Story Points:** 5
**Sprint:** Sprint 6
**Status:** Todo
**Branch:** `feature/polish-001-responsive-design`
**Assigned To:** react-architect

#### User Story
As a mobile user, I want the application to work smoothly on my device so that I can use it on the go.

#### Description
Ensure all components are responsive and optimized for mobile devices with appropriate breakpoints and touch interactions.

#### Acceptance Criteria
- Given I am on a mobile device
  When I view any page
  Then the layout should adapt to the smaller screen size

- Given touch interactions on mobile
  When I tap buttons or links
  Then they should have appropriate touch targets (minimum 44x44px)

- Given the navigation on mobile
  When I view it
  Then it should collapse into a hamburger menu or bottom navigation

- Given forms on mobile
  When I interact with them
  Then keyboards should appear appropriately and fields should be easily tappable

- Given images in the feed
  When I view them on mobile
  Then they should scale appropriately without overflowing

#### Technical Requirements
- Responsive breakpoints: mobile (<640px), tablet (640-1024px), desktop (>1024px)
- Touch-friendly button sizes
- Mobile navigation pattern (hamburger or bottom nav)
- Optimized image sizes for mobile
- Test on real devices and browser dev tools
- Consider mobile-first CSS approach

#### Design Decisions
- Mobile-first approach
- Bottom navigation for mobile (common pattern in social apps)
- Hamburger menu for secondary navigation
- Stack layouts on mobile, grid on desktop
- Larger touch targets on mobile

#### Implementation Notes
Update files:
- All component files - Add responsive styles
- `src/components/layout/Navigation.tsx` - Mobile nav
- `src/components/layout/MobileBottomNav.tsx` - Bottom navigation
- Add global responsive utilities

Use Tailwind responsive classes or CSS media queries:
```css
/* Mobile first */
.container {
  padding: 1rem;
}

/* Tablet */
@media (min-width: 640px) {
  .container {
    padding: 2rem;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .container {
    padding: 3rem;
  }
}
```

#### Testing Checklist
- [ ] Test on mobile devices (iOS and Android)
- [ ] Test on tablets
- [ ] Test on desktop
- [ ] Navigation works on all screen sizes
- [ ] Touch targets appropriately sized
- [ ] Forms usable on mobile
- [ ] Images scale correctly
- [ ] No horizontal scrolling
- [ ] Performance good on mobile devices

#### Related Stories
- Depends on: All feature stories
- Related to: POLISH-002

---

### Story Card: POLISH-002 - Loading States and Error Handling

**Priority:** High
**Story Points:** 3
**Sprint:** Sprint 6
**Status:** Todo
**Branch:** `feature/polish-002-loading-error-states`
**Assigned To:** react-architect

#### User Story
As a user, I want to see clear feedback when data is loading or when errors occur so that I understand what's happening.

#### Description
Implement consistent loading skeletons, spinners, and error messages throughout the application.

#### Acceptance Criteria
- Given any data fetching operation
  When data is loading
  Then I should see a skeleton loader or spinner

- Given a failed API request
  When an error occurs
  Then I should see a user-friendly error message with retry option

- Given a form submission
  When it's processing
  Then the submit button should show a loading state and be disabled

- Given an empty state (no posts, no followers, etc.)
  When I view it
  Then I should see a helpful message with suggested actions

- Given a network error
  When I try to perform an action
  Then I should see an error message explaining the issue

#### Technical Requirements
- Consistent loading skeleton components
- Error boundary for catching React errors
- Toast notifications for success/error messages
- Retry functionality for failed requests
- Form submission loading states
- Empty state components
- Network error detection and messaging

#### Design Decisions
- Skeleton loaders for content (better UX than spinners)
- Toast notifications for transient feedback
- Error boundaries catch unhandled errors
- Retry buttons on error states
- Helpful empty states guide users

#### Implementation Notes
Create files:
- `src/components/common/SkeletonLoader.tsx` - Skeleton components
- `src/components/common/ErrorBoundary.tsx` - Error boundary
- `src/components/common/ErrorMessage.tsx` - Error display
- `src/components/common/EmptyState.tsx` - Empty states
- `src/components/common/Toast.tsx` - Toast notifications
- `src/hooks/useToast.ts` - Toast hook

Libraries to add:
- `react-hot-toast` or `sonner` for toast notifications

#### Testing Checklist
- [ ] Loading states display during fetching
- [ ] Error messages show on failures
- [ ] Retry functionality works
- [ ] Empty states helpful and actionable
- [ ] Form loading states prevent double submission
- [ ] Error boundary catches errors
- [ ] Toast notifications appear and dismiss
- [ ] All states tested across features

#### Related Stories
- Depends on: All feature stories
- Related to: None

---

### Story Card: POLISH-003 - Performance Optimization

**Priority:** Medium
**Story Points:** 3
**Sprint:** Sprint 6
**Status:** Todo
**Branch:** `feature/polish-003-performance`
**Assigned To:** react-architect

#### User Story
As a user, I want the application to load quickly and run smoothly so that I have a good experience.

#### Description
Optimize application performance through code splitting, lazy loading, image optimization, and React performance best practices.

#### Acceptance Criteria
- Given the initial page load
  When I visit the site
  Then the first contentful paint should occur within 2 seconds

- Given images in the feed
  When I scroll
  Then images should lazy load as they come into view

- Given the application bundle
  When analyzed
  Then it should be split into appropriate chunks with no single chunk over 500KB

- Given React component rendering
  When re-renders occur
  Then only necessary components should re-render

- Given the Lighthouse performance score
  When tested
  Then it should score above 90 on mobile and desktop

#### Technical Requirements
- Code splitting with React.lazy and Suspense
- Route-based code splitting
- Image lazy loading
- Memoization with React.memo, useMemo, useCallback
- Virtualization for long lists
- Bundle analysis and optimization
- Compress images before upload
- Use appropriate image formats (WebP)

#### Design Decisions
- Route-based code splitting for faster initial load
- Lazy load images and media
- Virtualize long feeds (if needed)
- Memoize expensive computations
- Debounce/throttle frequent operations

#### Implementation Notes
Update files:
- `src/App.tsx` - Add React.lazy for route components
- All list components - Add virtualization if needed
- Image components - Add lazy loading
- Expensive components - Add memoization

Libraries to consider:
- `react-window` or `react-virtualized` for list virtualization
- `react-lazy-load-image-component` for image lazy loading

Example:
```typescript
// Lazy load route components
const HomePage = React.lazy(() => import('./pages/HomePage'));
const ProfilePage = React.lazy(() => import('./pages/ProfilePage'));

// In App.tsx
<Suspense fallback={<LoadingSpinner />}>
  <Routes>
    <Route path="/" element={<HomePage />} />
    <Route path="/profile/:username" element={<ProfilePage />} />
  </Routes>
</Suspense>
```

#### Testing Checklist
- [ ] Lighthouse score above 90
- [ ] Bundle analyzed and optimized
- [ ] Code splitting reduces initial bundle size
- [ ] Images lazy load correctly
- [ ] No unnecessary re-renders
- [ ] Long lists perform well
- [ ] Application feels fast and responsive
- [ ] Memory leaks addressed

#### Related Stories
- Depends on: All feature stories
- Related to: POLISH-001

---

## Epic 13: Security & Quality Assurance

### Story Card: SEC-001 - Security Audit

**Priority:** High
**Story Points:** 5
**Sprint:** Sprint 7
**Status:** Todo
**Branch:** N/A (Review task)
**Assigned To:** react-security-auditor

#### User Story
As a project stakeholder, I need the application to be secure so that user data is protected and vulnerabilities are minimized.

#### Description
Conduct comprehensive security audit of the application covering authentication, authorization, data validation, XSS, CSRF, and other common vulnerabilities.

#### Acceptance Criteria
- Given the authentication system
  When audited
  Then it should follow security best practices (secure session management, password hashing via Supabase)

- Given user inputs
  When validated
  Then they should be sanitized to prevent XSS attacks

- Given API requests
  When sent
  Then they should include proper authentication tokens

- Given file uploads
  When validated
  Then they should check file types, sizes, and sanitize filenames

- Given the RLS policies
  When tested
  Then they should properly restrict data access based on user permissions

- Given environment variables
  When checked
  Then sensitive keys should not be exposed in client-side code

#### Technical Requirements
- Review all user input handling
- Check for XSS vulnerabilities
- Verify CSRF protection (Supabase handles this)
- Validate file uploads properly
- Test RLS policies with different user roles
- Review authentication flows
- Check for exposed secrets
- Dependency security audit (npm audit)

#### Design Decisions
- Use Supabase's built-in security features
- Sanitize all user inputs
- Validate on both client and server
- Never trust client-side validation alone
- Follow OWASP security guidelines

#### Implementation Notes
Security checklist:
- [ ] Run `npm audit` and fix vulnerabilities
- [ ] Review all RLS policies in Supabase
- [ ] Check for XSS vulnerabilities in user-generated content
- [ ] Validate file upload security
- [ ] Ensure environment variables not exposed
- [ ] Review authentication flows
- [ ] Check for SQL injection vulnerabilities (Supabase client prevents this)
- [ ] Test authorization boundaries

Create security report document with findings and recommendations.

#### Testing Checklist
- [ ] XSS attack attempts blocked
- [ ] File upload validation works
- [ ] RLS policies enforce correct access
- [ ] No secrets in client code
- [ ] All dependencies up to date and secure
- [ ] Authentication flows secure
- [ ] CSRF protection in place
- [ ] Security report created

#### Related Stories
- Depends on: All feature stories
- Blocks: Final deployment

---

### Story Card: QA-001 - Quality Assurance Testing

**Priority:** High
**Story Points:** 5
**Sprint:** Sprint 7
**Status:** Todo
**Branch:** N/A (Testing task)
**Assigned To:** react-qa-auditor

#### User Story
As a user, I need the application to work correctly so that I can use all features without encountering bugs.

#### Description
Comprehensive QA testing of all features, user flows, edge cases, and cross-browser compatibility.

#### Acceptance Criteria
- Given all user stories
  When tested
  Then all acceptance criteria should be met

- Given all user flows
  When tested end-to-end
  Then they should work without errors

- Given edge cases
  When tested
  Then they should be handled gracefully

- Given multiple browsers
  When tested
  Then the application should work consistently

- Given accessibility standards (WCAG 2.1 AA)
  When tested
  Then the application should meet basic accessibility requirements

#### Technical Requirements
- Test all user stories and acceptance criteria
- End-to-end testing of critical flows
- Cross-browser testing (Chrome, Firefox, Safari, Edge)
- Mobile testing (iOS Safari, Chrome Android)
- Accessibility testing
- Performance testing
- Edge case testing

#### Design Decisions
- Focus on critical user flows first
- Document all bugs found
- Prioritize bugs by severity
- Retest after fixes

#### Implementation Notes
Testing areas:
1. Authentication (register, login, logout, session management)
2. Posts (create, edit, delete, view, media upload)
3. Comments (create, edit, delete, view)
4. Reactions (like, dislike, change, remove)
5. Follows (follow, unfollow, requests, approve/deny)
6. Feeds (chronological, popular, filtering)
7. Notifications (receive, read, clear)
8. Privacy (private accounts, post visibility)
9. Search (user search, results)
10. Profile (view, edit, avatar upload)

Create QA report document with:
- Test cases executed
- Bugs found (with severity)
- Browser compatibility results
- Accessibility issues
- Performance metrics
- Recommendations

#### Testing Checklist
- [ ] All user stories tested
- [ ] Critical flows work end-to-end
- [ ] Edge cases handled
- [ ] Cross-browser compatibility verified
- [ ] Mobile experience tested
- [ ] Accessibility audit completed
- [ ] Performance benchmarks met
- [ ] QA report created
- [ ] Bugs documented and prioritized

#### Related Stories
- Depends on: All feature stories
- Related to: SEC-001

---

## Summary

### Total Story Points: 93

### Sprint Breakdown:

**Sprint 1 (Database & Foundation):** 10 points
- DB-001: Supabase Configuration (2)
- FE-001: React Project Initialization (3)
- AUTH-001: User Registration and Login (5)

**Sprint 2 (Core Posts):** 13 points
- AUTH-002: Profile Management (3)
- POST-001: Create and View Posts (5)
- POST-002: Media Uploads (5)
- FEED-001: Chronological Feed (included in POST-001, refinement)

**Sprint 3 (Engagement):** 13 points
- POST-003: Edit Posts (3)
- POST-004: Delete Posts (2)
- COMM-001: Create Comments (5)
- REACT-001: Post Reactions (3)

**Sprint 4 (Social Features):** 12 points
- COMM-002: Edit/Delete Comments (2)
- REACT-002: Comment Reactions (2)
- FOLLOW-001: Follow/Unfollow (3)
- FOLLOW-002: Follow Requests (3)
- PRIV-001: Private Accounts (3)
- FEED-002: Popularity Feed (3)

**Sprint 5 (Advanced Features):** 12 points
- NOTIF-001: Notification System (5)
- HIST-001: Edit History Viewer (2)
- SEARCH-001: User Search (3)

**Sprint 6 (Polish):** 11 points
- POLISH-001: Responsive Design (5)
- POLISH-002: Loading/Error States (3)
- POLISH-003: Performance (3)

**Sprint 7 (Security & QA):** 10 points
- SEC-001: Security Audit (5)
- QA-001: QA Testing (5)

### Dependencies Graph:
```
DB-001 (Database Setup)
  └─> FE-001 (React Init)
      └─> AUTH-001 (Auth)
          └─> AUTH-002 (Profile)
              ├─> POST-001 (Posts)
              │   ├─> POST-002 (Media)
              │   │   └─> POST-003 (Edit)
              │   ├─> POST-004 (Delete)
              │   ├─> COMM-001 (Comments)
              │   │   ├─> COMM-002 (Edit/Delete Comments)
              │   │   └─> REACT-002 (Comment Reactions)
              │   ├─> REACT-001 (Reactions)
              │   │   └─> FEED-002 (Popular Feed)
              │   ├─> FEED-001 (Chronological)
              │   └─> HIST-001 (Edit History)
              ├─> FOLLOW-001 (Follows)
              │   ├─> FOLLOW-002 (Follow Requests)
              │   └─> PRIV-001 (Privacy)
              ├─> SEARCH-001 (Search)
              └─> NOTIF-001 (Notifications)

All Features
  └─> POLISH-001, POLISH-002, POLISH-003 (Polish)
      └─> SEC-001, QA-001 (Security & QA)
```
