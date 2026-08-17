# Discourse Platform

🚀 A modern, high-performance communication and community platform inspired by next-generation design principles.

## Overview

Discourse Platform is a full-featured communication suite built for **speed, reliability, customization, and user experience**. It combines real-time messaging, voice/video communication, community management, and collaboration tools into a single, performant platform.

## Key Features

### Core Communication
- ✅ Real-time text messaging with threads and replies
- ✅ Voice channels with WebRTC optimization
- ✅ 1-to-1 and group voice calls
- ✅ Direct messaging
- ✅ Rich message formatting (markdown, code blocks, embeds)
- ✅ Message reactions, pinning, and bookmarking

### Community & Organization
- ✅ Unlimited communities with custom branding
- ✅ Multiple channel types (text, voice, announcements, forums)
- ✅ Role-based access control with granular permissions
- ✅ Community analytics and insights
- ✅ Invite management with expiration and usage limits

### User Experience
- ✅ Light/dark/system themes with custom accent colors
- ✅ Responsive design for desktop, tablet, and mobile
- ✅ Keyboard shortcuts and command palette
- ✅ Comprehensive search across messages, users, and communities
- ✅ Intelligent notification system with customization

### Performance First
- ✅ Lazy loading and code splitting
- ✅ Message virtualization for large histories
- ✅ Efficient caching with Redis
- ✅ WebSocket-based real-time updates
- ✅ Graceful offline handling and reconnection
- ✅ Optimized for low-bandwidth connections

### Safety & Moderation
- ✅ Comprehensive moderation tools
- ✅ User reporting system
- ✅ Block and mute functionality
- ✅ Audit logs and activity tracking
- ✅ Content filtering and rate limiting

## Architecture

### Tech Stack

**Frontend:**
- Next.js with React 18+
- TypeScript
- TailwindCSS for styling
- Zustand for state management
- TanStack Query for data fetching
- Socket.IO for real-time communication
- WebRTC for voice/video

**Backend:**
- Node.js with Express
- PostgreSQL for persistent data
- Redis for caching and real-time coordination
- Socket.IO for WebSocket communication
- Bull for job queues
- Docker for containerization

**Infrastructure:**
- AWS S3/CloudFront for media storage and CDN
- Docker Compose for local development
- GitHub Actions for CI/CD
- Prometheus + Grafana for monitoring

## Project Structure

```
discourse-platform/
├── apps/
│   ├── web/              # Next.js frontend application
│   ├── server/           # Express backend API
│   └── mobile/           # React Native mobile app (future)
├── packages/
│   ├── types/            # Shared TypeScript types
│   ├── ui/               # Reusable UI component library
│   └── utils/            # Shared utilities and helpers
├── infrastructure/       # Docker, deployment configs
├── docs/                 # Architecture and design docs
└── scripts/              # Development and deployment scripts
```

## Getting Started

### Prerequisites
- Node.js 18+
- PostgreSQL 14+
- Redis 7+
- Docker & Docker Compose (recommended)

### Installation

```bash
# Clone the repository
git clone https://github.com/vitorvitin786/discourse-platform.git
cd discourse-platform

# Install dependencies
npm install

# Set up environment variables
cp .env.example .env.local

# Start development services
docker-compose up -d

# Run database migrations
npm run db:migrate

# Start development server
npm run dev
```

The application will be available at `http://localhost:3000`.

## Development

### Running Tests
```bash
npm run test
npm run test:e2e
```

### Building for Production
```bash
npm run build
npm start
```

### Database Migrations
```bash
npm run db:migrate
npm run db:seed
```

## API Documentation

API docs are available at `/api/docs` when the server is running, or see [API.md](./docs/API.md).

## Architecture Decision Records (ADRs)

See [docs/ADRs/](./docs/ADRs/) for detailed architecture decisions.

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md) for guidelines.

## Performance Benchmarks

- Message load time: < 100ms
- Search response: < 200ms
- Voice call connection: < 2 seconds
- Memory usage (idle): < 150MB
- Supports communities with 10,000+ concurrent members

## Roadmap

### Phase 1 (MVP)
- [x] Authentication & profiles
- [x] Communities and channels
- [x] Real-time messaging
- [x] Voice channels
- [x] Roles and permissions
- [x] Direct messages
- [x] Notifications
- [x] Search
- [x] Moderation basics
- [x] Responsive UI

### Phase 2 (Enhancement)
- [ ] Video calls and screen sharing
- [ ] Events and calendar
- [ ] Collaborative documents
- [ ] Advanced moderation
- [ ] Community analytics
- [ ] AI features (assistant, summarization)

### Phase 3 (Platform)
- [ ] Public developer API
- [ ] Bot accounts and webhooks
- [ ] Slash commands and apps
- [ ] Plugin architecture
- [ ] Monetization features

## License

MIT

## Support

For issues, questions, or suggestions, please open an issue on GitHub.
