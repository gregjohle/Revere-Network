# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Revere Network** - A Twitter/X clone built with React and Supabase

**GitHub Repository**: https://github.com/gregjohle/Revere-Network

This is a social media application that allows users to create posts, comment, like/dislike content, follow other users, and view content through chronological or popularity-based feeds.

## Current Status: Planning Complete, Awaiting Database Setup

**Phase**: Planning & Documentation Complete
**Next Step**: User is setting up Supabase database, then implementation begins

### What's Been Completed
- Complete database schema designed (9 tables with RLS policies)
- 25 story cards created across 13 epics (93 story points)
- 7-sprint implementation roadmap
- Team workflow and development process documented
- Git repository initialized and pushed to GitHub
- Material-UI selected as UI library
- GitHub CLI configured and authenticated

### Awaiting
- User to set up Supabase database using schema in `docs/revere-network-schema-final.md`
- User to provide Supabase credentials (URL and Anon Key)

## Tech Stack

### Frontend
- React 18 with TypeScript
- Vite for build tooling
- Material-UI (MUI) for UI components
- TanStack Query for server state management
- React Router for navigation

### Backend
- Supabase (PostgreSQL)
- Supabase Auth for authentication
- Supabase Storage for media files
- Supabase Realtime for notifications
- Row Level Security (RLS) for data protection

## Project Structure

```
/Users/greg/AI-Playground/
├── .claude/agents/          # Custom Claude Code agents (local only, gitignored)
│   ├── react-architect.md
│   ├── react-project-manager.md
│   ├── react-qa-auditor.md
│   ├── react-review-board.md
│   └── react-security-auditor.md
├── docs/
│   ├── revere-network-schema-final.md    # Database schema with SQL
│   ├── revere-network-story-cards.md     # All 25 story cards
│   ├── TEAM_WORKFLOW.md                  # Development process
│   └── IMPLEMENTATION_PLAN.md            # Sprint roadmap
├── README.md                 # Project overview
├── PROJECT_SUMMARY.md        # Executive summary
├── .env.example              # Environment template (placeholders only)
└── .gitignore                # Git ignore rules
```

## Development Workflow

This project uses a multi-agent team approach:

- **react-project-manager**: Coordinates team, manages workflow, creates PRs
- **react-architect**: Implements features, writes tests, handles development
- **react-review-board**: Reviews PRs, provides technical guidance
- **react-security-auditor**: Reviews security vulnerabilities
- **react-qa-auditor**: Tests features against acceptance criteria

### Git Workflow
1. Feature branches created from `main`
2. Implement story card on feature branch
3. Create PR when ready
4. react-review-board reviews code
5. react-security-auditor audits security
6. react-qa-auditor tests functionality
7. Merge to main after approval

## Key Features

1. **Authentication**: Email/password registration and login via Supabase Auth
2. **Posts**: Create, edit, delete posts (500 char limit) with media support
3. **Comments**: Comment on posts (500 char limit)
4. **Reactions**: Like/dislike posts and comments
5. **Follow System**: Follow users, private accounts with request approval
6. **Feeds**: Chronological and popularity-based feeds
7. **Notifications**: Real-time notifications for interactions
8. **Edit History**: Track all post edits
9. **Privacy**: RLS policies, private accounts

## Commands (Will be added after React project initialization)

Commands will be added in Story FE-001 when the React project is initialized.

Expected commands:
- `npm install` - Install dependencies
- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run test` - Run tests
- `npm run lint` - Lint code

## Important Notes

### Repository & Version Control
- GitHub repository: https://github.com/gregjohle/Revere-Network
- `.claude/` folder is gitignored (local agent configurations only)
- `.env` files are gitignored (secrets remain secret)
- `.env.example` contains placeholders only for developer reference

### Database Schema
- Usernames are case-insensitive (stored lowercase via trigger)
- Posts and comments limited to 500 characters
- Media stored in Supabase Storage (avatars and post-media buckets)
- Edit history automatically tracked via database triggers
- Notifications automatically created via database triggers

### Development Priorities
- Following the 7-sprint roadmap in `docs/IMPLEMENTATION_PLAN.md`
- Implementing stories in order per epic
- Maintaining 80%+ test coverage
- Following Material-UI design patterns
- Security-first approach with RLS policies

### Story Cards Reference
All story cards are in `docs/revere-network-story-cards.md` with:
- User stories
- Acceptance criteria (Given-When-Then format)
- Technical requirements
- Implementation notes
- Testing checklists

## Next Steps (In Order)

1. **User Action**: Set up Supabase database
   - Create project at supabase.com
   - Execute SQL from `docs/revere-network-schema-final.md`
   - Set up storage buckets (avatars, post-media)
   - Provide credentials to team

2. **Story FE-001**: React Project Initialization
   - react-architect creates Vite + React + TypeScript project
   - Install Material-UI and dependencies
   - Configure Supabase client
   - Set up routing and folder structure

3. **Story AUTH-001**: User Registration and Login
   - Implement authentication UI
   - Connect to Supabase Auth
   - Create user profiles

## Resources

- [Supabase Documentation](https://supabase.com/docs)
- [React Documentation](https://react.dev)
- [Material-UI Documentation](https://mui.com)
- [TanStack Query Documentation](https://tanstack.com/query)

## Contact

For questions about project planning or to start implementation, work with the react-project-manager agent.
