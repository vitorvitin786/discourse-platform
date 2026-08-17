# Architecture Overview

## System Design

Discourse Platform is designed as a modern, scalable communication platform with a clear separation between frontend and backend services.

### High-Level Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                  Client Applications                         │
│  ┌─────────────────────────────┐ ┌─────────────────────────┐ │
│  │    Web (Next.js)           │ │  Mobile (React)         │ │
│  └─────────────────────────────┘ └─────────────────────────┘ │
└─────────────────────┬──────────────────────────────────────┬─┘
        HTTP + WebSocket              HTTP + WebSocket
                     │                                 │
┌─────────────────────────────────────────────────────────────┐
│               API Gateway / Load Balancer                  │
└─────────────────────────────────────────────────────────────┘
                     │
┌─────────────────────────────────────────────────────────────┐
│                   Backend Services                          │
│  ┌──────────────────┐ ┌──────────────────┐ ┌──────────────┐ │
│  │ Auth Service     │ │ Chat Service     │ │Voice Service │ │
│  └──────────────────┘ └──────────────────┘ └──────────────┘ │
│  ┌──────────────────┐ ┌──────────────────┐ ┌──────────────┐ │
│  │ User Service     │ │ Community Svc    │ │Moderation Svc│ │
│  └──────────────────┘ └──────────────────┘ └──────────────┘ │
└─────────────────────┬──────────────────┬──────────────────┬──┘
    │                  │                  │
┌────────────────┐ ┌────────────────┐ ┌────────────────┐
│PostgreSQL      │ │  Redis         │ │  S3/CDN        │
│                │ │                │ │                │
└────────────────┘ └────────────────┘ └────────────────┘
```

## Technology Stack

### Frontend
- **Framework**: Next.js 14+ (React 18+)
- **Language**: TypeScript
- **State Management**: Zustand
- **Data Fetching**: TanStack Query
- **Styling**: TailwindCSS + CSS-in-JS
- **Real-time**: Socket.IO client
- **Media**: WebRTC
- **Testing**: Vitest, Playwright

### Backend
- **Runtime**: Node.js 18+
- **Framework**: Express.js
- **Language**: TypeScript
- **Database**: PostgreSQL 14+
- **Cache**: Redis 7+
- **Message Queue**: Bull (Redis-backed)
- **WebSocket**: Socket.IO
- **Media Storage**: S3-compatible (MinIO/AWS)
- **Testing**: Jest, Supertest

### Infrastructure
- **Containerization**: Docker
- **Orchestration**: Docker Compose (dev), Kubernetes (prod)
- **CI/CD**: GitHub Actions
- **Monitoring**: Prometheus + Grafana
- **Logging**: ELK Stack (optional)
- **CDN**: CloudFront (AWS) or similar

## Database Schema (Core Tables)

### Users
```sql
users {
  id: UUID primary key
  email: string unique
  username: string unique
  password_hash: string
  avatar_url: string
  banner_url: string
  bio: text
  status: enum (online, idle, busy, invisible)
  custom_status: string
  pronouns: string
  verified: boolean
  created_at: timestamp
  updated_at: timestamp
}
```

### Communities
```sql
communities {
  id: UUID primary key
  owner_id: UUID FK users
  name: string
  slug: string unique
  description: text
  icon_url: string
  banner_url: string
  visibility: enum (public, private)
  member_count: int
  created_at: timestamp
  updated_at: timestamp
}
```

### Channels
```sql
channels {
  id: UUID primary key
  community_id: UUID FK communities
  name: string
  type: enum (text, voice, video, forum, announcement)
  description: text
  position: int
  archived: boolean
  created_at: timestamp
  updated_at: timestamp
}
```

### Messages
```sql
messages {
  id: UUID primary key
  channel_id: UUID FK channels
  author_id: UUID FK users
  content: text
  mentions: json array
  reactions: jsonb
  thread_parent_id: UUID FK messages (nullable)
  edited_at: timestamp
  deleted_at: timestamp (soft delete)
  created_at: timestamp
  updated_at: timestamp
}

-- Indexes
CREATE INDEX idx_messages_channel_id_created_at ON messages(channel_id, created_at DESC);
CREATE INDEX idx_messages_author_id ON messages(author_id);
CREATE INDEX idx_messages_thread_parent_id ON messages(thread_parent_id);
```

### Memberships
```sql
memberships {
  id: UUID primary key
  user_id: UUID FK users
  community_id: UUID FK communities
  joined_at: timestamp
  left_at: timestamp (nullable)
  role_id: UUID FK roles
  muted: boolean
  notifications_enabled: boolean
  unique(user_id, community_id)
}
```

### Roles & Permissions
```sql
roles {
  id: UUID primary key
  community_id: UUID FK communities
  name: string
  color: string
  position: int
  permissions: jsonb array
  created_at: timestamp
}

role_assignments {
  id: UUID primary key
  user_id: UUID FK users
  role_id: UUID FK roles
  community_id: UUID FK communities
  assigned_at: timestamp
}
```

## API Architecture

### RESTful Endpoints
```
Authentication
  POST   /api/auth/register
  POST   /api/auth/login
  POST   /api/auth/refresh
  POST   /api/auth/logout

Users
  GET    /api/users/me
  GET    /api/users/:id
  PATCH  /api/users/me
  GET    /api/users/search?q=query

Communities
  GET    /api/communities
  POST   /api/communities
  GET    /api/communities/:id
  PATCH  /api/communities/:id
  DELETE /api/communities/:id
  GET    /api/communities/:id/members
  POST   /api/communities/:id/members
  DELETE /api/communities/:id/members/:userId

Channels
  GET    /api/communities/:id/channels
  POST   /api/communities/:id/channels
  PATCH  /api/channels/:id
  DELETE /api/channels/:id

Messages
  GET    /api/channels/:id/messages?limit=50&offset=0
  POST   /api/channels/:id/messages
  PATCH  /api/messages/:id
  DELETE /api/messages/:id
  GET    /api/channels/:id/threads/:threadId/messages
```

### WebSocket Events
```
Authentication
  connect (with JWT token)
  disconnect

Presence
  user:online
  user:offline
  user:status_change

Messaging
  message:create
  message:update
  message:delete
  message:reaction
  typing:start
  typing:stop

Voice
  voice:user_joined
  voice:user_left
  voice:user_speaking
```

## Caching Strategy

### Redis Cache Layers

1. **Session Cache** (TTL: 24h)
   - User sessions
   - Authentication tokens

2. **User Cache** (TTL: 1h)
   - User profiles
   - Online status
   - User settings

3. **Community Cache** (TTL: 30m)
   - Community info
   - Member list
   - Roles and permissions

4. **Message Cache** (TTL: 5m)
   - Recent messages (last 100 per channel)
   - Message reactions
   - Typing indicators

5. **Search Cache** (TTL: 10m)
   - Popular search queries
   - User search results

### Cache Invalidation
- Write-through for critical data
- Event-based invalidation for real-time updates
- TTL-based expiration for non-critical data

## Performance Optimizations

### Frontend
- Code splitting by route
- Lazy loading of components
- Image optimization with Next.js Image
- Virtual scrolling for message lists
- Request deduplication with TanStack Query
- Service Workers for offline support

### Backend
- Database query optimization with indexes
- Connection pooling with node-postgres
- Message pagination (cursor-based)
- Efficient caching strategies
- Request batching
- Rate limiting per user/IP

### Infrastructure
- CDN for static assets
- Horizontal scaling of services
- Load balancing
- Database read replicas
- Redis clustering for high availability

## Security Architecture

- **Authentication**: JWT with refresh tokens
- **Authorization**: Role-based access control (RBAC)
- **Data Encryption**: TLS in transit, encrypted at rest
- **Input Validation**: Server-side validation of all inputs
- **CORS**: Strict origin validation
- **Rate Limiting**: Per-endpoint rate limits
- **Audit Logging**: All moderation actions logged
- **2FA**: Optional two-factor authentication

## Deployment Strategy

### Development
- Docker Compose for local services
- Hot module reloading
- Debug logging enabled

### Staging
- Kubernetes deployment
- Full test suite execution
- Performance profiling

### Production
- Blue-green deployments
- Database migrations with rollback
- Monitoring and alerting
- Regular backups

## Scalability Considerations

- **Horizontal Scaling**: Services can scale independently
- **Database Sharding**: Ready for sharding by community_id
- **Message Queue**: Bull for async job processing
- **Caching**: Multi-layer Redis caching
- **CDN**: Offload static content delivery
- **WebSocket Scaling**: Multiple Socket.IO servers with Redis adapter

## Monitoring & Observability

- **Metrics**: Prometheus for application metrics
- **Visualization**: Grafana dashboards
- **Logging**: Structured JSON logging
- **Tracing**: OpenTelemetry for distributed tracing (optional)
- **Alerting**: Alert rules for SLOs

## Error Handling

- Centralized error handling middleware
- Graceful degradation for non-critical features
- User-friendly error messages
- Detailed error logging for debugging
- Sentry integration for error tracking (optional)
