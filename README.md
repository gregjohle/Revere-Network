# Revere Network

A Twitter/X clone built with React and Supabase.

## Features

- User authentication and profile management
- Create, edit, and delete posts with media support
- Comment on posts
- Like/dislike posts and comments
- Follow/unfollow users
- Private accounts with follow requests
- Chronological and popularity-based feeds
- Real-time notifications
- Post edit history
- User search

## Tech Stack

- **Frontend**: React 18 + TypeScript + Vite
- **Backend**: Supabase (PostgreSQL + Auth + Storage + Realtime)
- **State Management**: TanStack Query (React Query) + Context API
- **Routing**: React Router v6
- **Styling**: Tailwind CSS (or your chosen UI library)
- **Form Handling**: React Hook Form

## Project Structure

```
revere-network/
├── src/
│   ├── components/       # Reusable components
│   │   ├── auth/
│   │   ├── posts/
│   │   ├── comments/
│   │   ├── notifications/
│   │   └── common/
│   ├── pages/           # Page components
│   ├── hooks/           # Custom React hooks
│   ├── contexts/        # React contexts
│   ├── services/        # API services
│   ├── types/           # TypeScript types
│   ├── utils/           # Utility functions
│   └── App.tsx
├── docs/                # Documentation
│   ├── revere-network-schema-final.md
│   └── revere-network-story-cards.md
└── README.md
```

## Getting Started

### Prerequisites

- Node.js 18+ and npm
- Supabase account and project

### Database Setup

1. Create a new Supabase project at https://supabase.com
2. Follow the SQL setup instructions in `docs/revere-network-schema-final.md`
3. Execute all SQL commands in order in the Supabase SQL Editor
4. Set up storage buckets as described in the schema document

### Frontend Setup

```bash
# Install dependencies
npm install

# Create .env file with your Supabase credentials
cp .env.example .env

# Edit .env and add your Supabase URL and anon key

# Start development server
npm run dev
```

### Environment Variables

Create a `.env` file in the root directory:

```env
VITE_SUPABASE_URL=your_supabase_project_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
```

## Development Workflow

This project follows an agile sprint-based workflow with story cards. See `docs/revere-network-story-cards.md` for the complete breakdown.

### Team

- **react-architect**: Feature implementation
- **react-security-auditor**: Security reviews
- **react-qa-auditor**: Quality assurance
- **react-review-board**: PR reviews and technical guidance

### Branch Naming Convention

- Feature branches: `feature/STORY-ID-brief-description`
- Bugfix branches: `bugfix/issue-id-brief-description`

Example: `feature/auth-001-user-registration-login`

### Pull Request Process

1. Create feature branch from `main`
2. Implement story card requirements
3. Test thoroughly (meet all acceptance criteria)
4. Create PR with:
   - Link to story card
   - Description of changes
   - Testing performed
   - Screenshots (for UI changes)
5. Tag react-review-board for review
6. Address review feedback
7. Merge only after approval from senior developer

## Testing

```bash
# Run unit tests
npm run test

# Run tests with coverage
npm run test:coverage

# Run E2E tests (when implemented)
npm run test:e2e
```

## Building for Production

```bash
# Create production build
npm run build

# Preview production build locally
npm run preview
```

## Documentation

- [Database Schema](docs/revere-network-schema-final.md) - Complete database structure and setup
- [Story Cards](docs/revere-network-story-cards.md) - Feature breakdown and implementation guide

## Contributing

Please follow the story card workflow and ensure all acceptance criteria are met before submitting PRs.

## License

MIT
