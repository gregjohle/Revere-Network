# Revere Network - Team Workflow Guide

## Team Structure

### Project Manager (You)
- Breaks down features into story cards
- Coordinates team members
- Reviews completed work
- Manages git workflow and PRs
- Communicates with end user

### React Architect
- Implements features based on story cards
- Writes production-quality code
- Follows React best practices
- Creates unit and integration tests
- Documents implementation decisions

### React Security Auditor
- Reviews code for security vulnerabilities
- Validates authentication and authorization
- Checks input sanitization
- Reviews RLS policies
- Provides security recommendations

### React QA Auditor
- Tests all features against acceptance criteria
- Performs end-to-end testing
- Tests edge cases
- Verifies cross-browser compatibility
- Documents bugs and issues

### React Review Board
- Reviews pull requests
- Provides technical guidance
- Evaluates code quality and architecture
- Approves/requests changes on PRs
- Advises on technical decisions

## Development Process

### 1. Story Card Selection
- Project manager assigns story card to react-architect
- Story card includes:
  - User story and description
  - Acceptance criteria
  - Technical requirements
  - Implementation notes
  - Testing checklist

### 2. Implementation
- React architect creates feature branch from main
- Implements feature according to story card
- Writes tests for the feature
- Ensures all acceptance criteria are met
- Commits work with clear commit messages

### 3. Self-Testing
- Developer runs all tests
- Manually tests the feature
- Checks acceptance criteria
- Verifies no regressions

### 4. Security Review (if applicable)
- For features involving auth, data access, or user input
- React security auditor reviews code
- Identifies security concerns
- Developer addresses issues

### 5. Pull Request Creation
- Project manager creates PR when work is complete
- PR includes:
  - Link to story card
  - Summary of changes
  - Testing performed
  - Screenshots/videos for UI changes
  - Any breaking changes or notes

### 6. Code Review
- React review board reviews the PR
- Provides feedback on:
  - Code quality
  - Architecture
  - Performance
  - Best practices
  - Testing coverage
- Approval required before merge

### 7. QA Testing
- React QA auditor tests the feature
- Verifies all acceptance criteria
- Tests edge cases
- Reports any bugs found
- Developer fixes bugs, repeats review process

### 8. Merge and Deploy
- Project manager merges PR to main
- Updates story card status to "Done"
- Moves to next story card

## Git Workflow

### Branch Naming
- Feature: `feature/STORY-ID-brief-description`
- Bugfix: `bugfix/issue-id-brief-description`
- Hotfix: `hotfix/critical-issue-description`

### Commit Messages
```
Format: <type>(<scope>): <subject>

Types:
- feat: New feature
- fix: Bug fix
- refactor: Code refactoring
- test: Adding tests
- docs: Documentation
- style: Code formatting
- chore: Maintenance tasks

Example:
feat(auth): implement user registration with email validation

- Add RegisterForm component with validation
- Create auth service with registerUser function
- Add username availability check
- Include tests for registration flow
```

### Pull Request Template
```markdown
## Story Card
Link to story card: [AUTH-001](/docs/revere-network-story-cards.md#auth-001)

## Summary
Brief description of what this PR implements.

## Changes Made
- Bullet list of main changes
- Include new files, updated files
- Note any dependencies added

## Testing Performed
- [ ] All acceptance criteria met
- [ ] Unit tests written and passing
- [ ] Integration tests written and passing
- [ ] Manual testing completed
- [ ] No regressions found

## Screenshots/Videos
(For UI changes)

## Breaking Changes
None / Description of breaking changes

## Additional Notes
Any other context or information reviewers should know.
```

## Communication Guidelines

### Project Manager to Team
- Clear story card descriptions
- Specific acceptance criteria
- Provide context and background
- Answer questions promptly
- Acknowledge good work

### Team to Project Manager
- Ask questions when requirements unclear
- Report blockers immediately
- Provide status updates
- Document decisions made
- Suggest improvements

## Quality Standards

### Code Quality
- TypeScript strict mode enabled
- No console.log in production code
- Proper error handling
- Consistent naming conventions
- DRY principle followed

### Testing
- Minimum 80% code coverage
- All acceptance criteria have tests
- Edge cases covered
- Integration tests for critical flows

### Performance
- Lighthouse score > 90
- First contentful paint < 2s
- No unnecessary re-renders
- Optimized images and assets

### Accessibility
- WCAG 2.1 AA compliance
- Semantic HTML
- Keyboard navigation
- Screen reader support
- Proper ARIA labels

### Security
- No XSS vulnerabilities
- Proper input validation
- RLS policies enforced
- No exposed secrets
- Dependencies up to date

## Sprint Ceremonies

### Sprint Planning
- Review story cards for upcoming sprint
- Assign story points
- Identify dependencies
- Set sprint goals

### Daily Standup (as needed)
- What was completed yesterday
- What's planned for today
- Any blockers

### Sprint Review
- Demo completed features
- Get user feedback
- Update backlog

### Sprint Retrospective
- What went well
- What could be improved
- Action items for next sprint

## Tools and Resources

### Development
- Vite for build tooling
- TypeScript for type safety
- React Query for data fetching
- React Hook Form for forms

### Testing
- Vitest for unit testing
- React Testing Library for component tests
- Playwright or Cypress for E2E tests (when implemented)

### Code Quality
- ESLint for linting
- Prettier for formatting
- Husky for git hooks (optional)

### Collaboration
- Git for version control
- GitHub for code hosting and PRs
- Story cards for task management

## Troubleshooting

### Common Issues

**Problem**: Tests failing after dependency update
**Solution**: Check for breaking changes in changelog, update test mocks

**Problem**: Supabase RLS policy not working
**Solution**: Verify user is authenticated, check policy logic in Supabase dashboard

**Problem**: Build failing
**Solution**: Clear node_modules, reinstall dependencies, check TypeScript errors

**Problem**: Merge conflicts
**Solution**: Pull latest main, resolve conflicts, test thoroughly

## Getting Help

1. Check documentation first (README, schema, story cards)
2. Review similar implemented features
3. Ask specific questions with context
4. Provide error messages and steps to reproduce
5. Share relevant code snippets

## Success Criteria

A story is considered "Done" when:
- All acceptance criteria met
- All tests passing
- Code reviewed and approved
- QA tested and passed
- Security reviewed (if applicable)
- Merged to main
- No critical bugs reported
