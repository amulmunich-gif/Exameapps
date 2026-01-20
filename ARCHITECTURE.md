# Architecture & Deployment Guide

## System Architecture

### Overview
The Exam Practice Platform follows a **client-server architecture** with:
- **Flutter mobile app** (cross-platform)
- **RESTful API backend** (Node.js/Python/Java)
- **Firebase services** (Authentication, Storage, Analytics)
- **Database** (PostgreSQL/MongoDB/Firestore)
- **Payment processing** (Stripe)
- **CDN** (Cloudflare/AWS CloudFront)

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                     MOBILE CLIENTS                          │
│              Android | iOS | Web (Flutter)                  │
└────────────┬────────────────────────────────────────────────┘
             │
             │ HTTPS
             ▼
┌─────────────────────────────────────────────────────────────┐
│                      API GATEWAY                            │
│              (Load Balancer + Rate Limiting)                │
└────────────┬────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│                   BACKEND SERVICES                          │
│                                                             │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐          │
│  │   Auth     │  │   Exam     │  │  Payment   │          │
│  │  Service   │  │  Service   │  │  Service   │          │
│  └────────────┘  └────────────┘  └────────────┘          │
│                                                             │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐          │
│  │   User     │  │  Analytics │  │   Admin    │          │
│  │  Service   │  │  Service   │  │  Service   │          │
│  └────────────┘  └────────────┘  └────────────┘          │
└────────────┬────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│                     DATA LAYER                              │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐    │
│  │  PostgreSQL  │  │    Redis     │  │  Firebase    │    │
│  │   (Primary)  │  │   (Cache)    │  │  (Storage)   │    │
│  └──────────────┘  └──────────────┘  └──────────────┘    │
└─────────────────────────────────────────────────────────────┘

External Services:
├── Firebase Auth (User authentication)
├── Stripe (Payment processing)
├── Google AdMob (Advertisement)
├── Firebase Analytics (Usage tracking)
├── Cloudflare CDN (Static assets)
└── SendGrid/AWS SES (Email service)
```

## Technology Stack Details

### Frontend (Flutter)
```yaml
Core:
  - Flutter 3.x
  - Dart 3.x

State Management:
  - Riverpod (Global state)
  - Provider (Widget state)

Local Storage:
  - Hive (Structured data)
  - Shared Preferences (Simple key-value)
  - Flutter Secure Storage (Sensitive data)

Networking:
  - Dio (HTTP client)
  - Connectivity Plus (Network status)

UI/UX:
  - Material Design 3
  - Google Fonts
  - FL Chart (Charts)
  - Syncfusion (Advanced charts)
  - Cached Network Image (Image loading)
```

### Backend Options

#### Option 1: Node.js + Express
```javascript
// Recommended stack
- Node.js 18+
- Express.js
- TypeScript
- Prisma ORM
- PostgreSQL
- Redis
- JWT authentication
- Winston (Logging)
- Jest (Testing)
```

#### Option 2: Python + FastAPI
```python
# Alternative stack
- Python 3.11+
- FastAPI
- SQLAlchemy ORM
- PostgreSQL
- Redis
- JWT authentication
- Pydantic (Validation)
- Pytest (Testing)
```

#### Option 3: Java + Spring Boot
```java
// Enterprise stack
- Java 17+
- Spring Boot 3.x
- Spring Security
- JPA/Hibernate
- PostgreSQL
- Redis
- JWT authentication
- JUnit (Testing)
```

### Database Schema (PostgreSQL)

```sql
-- Users table
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    password_hash VARCHAR(255),
    user_type VARCHAR(50) NOT NULL,
    grade VARCHAR(50),
    school_board VARCHAR(100),
    subjects_of_interest TEXT[],
    profile_image_url TEXT,
    subscription_plan VARCHAR(50) DEFAULT 'free',
    subscription_expires_at TIMESTAMP,
    is_two_factor_enabled BOOLEAN DEFAULT false,
    is_email_verified BOOLEAN DEFAULT false,
    parent_email VARCHAR(255),
    created_at TIMESTAMP DEFAULT NOW(),
    last_login_at TIMESTAMP,
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Exams table
CREATE TABLE exams (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    title VARCHAR(255) NOT NULL,
    description TEXT,
    exam_type VARCHAR(50) NOT NULL,
    grade VARCHAR(50) NOT NULL,
    board VARCHAR(100) NOT NULL,
    subject VARCHAR(100) NOT NULL,
    duration_minutes INTEGER NOT NULL,
    total_questions INTEGER NOT NULL,
    passing_score INTEGER NOT NULL,
    is_premium BOOLEAN DEFAULT false,
    thumbnail_url TEXT,
    topics TEXT[] NOT NULL,
    difficulty VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Questions table
CREATE TABLE questions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    exam_id UUID REFERENCES exams(id) ON DELETE CASCADE,
    type VARCHAR(50) NOT NULL,
    question_text TEXT NOT NULL,
    image_url TEXT,
    options TEXT[],
    correct_answers TEXT[] NOT NULL,
    explanation TEXT,
    explanation_image_url TEXT,
    points INTEGER NOT NULL,
    difficulty VARCHAR(50) NOT NULL,
    topic VARCHAR(100) NOT NULL,
    order_index INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Exam attempts table
CREATE TABLE exam_attempts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    exam_id UUID REFERENCES exams(id) ON DELETE CASCADE,
    started_at TIMESTAMP NOT NULL,
    completed_at TIMESTAMP,
    score INTEGER NOT NULL DEFAULT 0,
    total_questions INTEGER NOT NULL,
    correct_answers INTEGER NOT NULL DEFAULT 0,
    time_spent_seconds INTEGER NOT NULL DEFAULT 0,
    topic_scores JSONB,
    answers JSONB NOT NULL,
    is_completed BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Subscriptions table
CREATE TABLE subscriptions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    plan VARCHAR(50) NOT NULL,
    stripe_subscription_id VARCHAR(255),
    stripe_customer_id VARCHAR(255),
    status VARCHAR(50) NOT NULL,
    current_period_start TIMESTAMP NOT NULL,
    current_period_end TIMESTAMP NOT NULL,
    cancel_at_period_end BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Create indexes for performance
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_exams_grade ON exams(grade);
CREATE INDEX idx_exams_board ON exams(board);
CREATE INDEX idx_questions_exam_id ON questions(exam_id);
CREATE INDEX idx_exam_attempts_user_id ON exam_attempts(user_id);
CREATE INDEX idx_exam_attempts_exam_id ON exam_attempts(exam_id);
CREATE INDEX idx_subscriptions_user_id ON subscriptions(user_id);
```

## Deployment Guide

### 1. Backend Deployment (AWS Example)

#### AWS Architecture
```
ECS/EKS (Containers)
├── Application Load Balancer
├── Auto Scaling Group
├── RDS PostgreSQL (Multi-AZ)
├── ElastiCache Redis
├── S3 (File storage)
└── CloudWatch (Monitoring)
```

#### Deployment Steps
```bash
# 1. Build Docker image
docker build -t exam-api:latest .

# 2. Push to ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com
docker tag exam-api:latest <account-id>.dkr.ecr.us-east-1.amazonaws.com/exam-api:latest
docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/exam-api:latest

# 3. Deploy to ECS
aws ecs update-service --cluster exam-cluster --service exam-api --force-new-deployment
```

### 2. Mobile App Deployment

#### Android (Google Play)
```bash
# 1. Update version in pubspec.yaml
version: 1.0.1+2

# 2. Build release bundle
flutter build appbundle --release

# 3. Upload to Play Console
# Navigate to: https://play.google.com/console
# Upload: build/app/outputs/bundle/release/app-release.aab
```

#### iOS (App Store)
```bash
# 1. Update version in pubspec.yaml
version: 1.0.1+2

# 2. Build release
flutter build ios --release

# 3. Open in Xcode and archive
open ios/Runner.xcworkspace

# 4. Upload via Xcode or Transporter
```

### 3. CI/CD Pipeline (GitHub Actions)

```yaml
# .github/workflows/deploy.yml
name: Deploy App

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.16.0'
      
      - name: Install dependencies
        run: flutter pub get
      
      - name: Run tests
        run: flutter test
      
      - name: Build Android APK
        run: flutter build apk --release
      
      - name: Build Android App Bundle
        run: flutter build appbundle --release
      
      - name: Upload to Play Store
        uses: r0adkll/upload-google-play@v1
        with:
          serviceAccountJsonPlainText: ${{ secrets.SERVICE_ACCOUNT_JSON }}
          packageName: com.examplatform.app
          releaseFiles: build/app/outputs/bundle/release/app-release.aab
          track: production
```

### 4. Environment Configuration

#### Development
```env
# .env.development
API_BASE_URL=http://localhost:3000
STRIPE_PUBLISHABLE_KEY=pk_test_xxx
FIREBASE_PROJECT_ID=exam-platform-dev
ADMOB_APP_ID=ca-app-pub-test-xxx
```

#### Production
```env
# .env.production
API_BASE_URL=https://api.examplatform.com
STRIPE_PUBLISHABLE_KEY=pk_live_xxx
FIREBASE_PROJECT_ID=exam-platform-prod
ADMOB_APP_ID=ca-app-pub-xxx
```

## Monitoring & Analytics

### Firebase Analytics Events
```dart
// Track custom events
await FirebaseAnalytics.instance.logEvent(
  name: 'exam_started',
  parameters: {
    'exam_id': examId,
    'exam_type': examType,
    'user_grade': userGrade,
  },
);

await FirebaseAnalytics.instance.logEvent(
  name: 'exam_completed',
  parameters: {
    'exam_id': examId,
    'score': score,
    'time_spent': timeSpent,
  },
);
```

### Crashlytics Integration
```dart
// Log non-fatal errors
FirebaseCrashlytics.instance.recordError(
  error,
  stackTrace,
  reason: 'API call failed',
);

// Log custom keys
FirebaseCrashlytics.instance.setCustomKey('user_id', userId);
FirebaseCrashlytics.instance.setCustomKey('exam_id', examId);
```

## Performance Optimization

### App Performance
1. **Image Optimization**
   - Use WebP format
   - Implement progressive loading
   - Cache aggressively

2. **Code Splitting**
   - Lazy load screens
   - Use deferred loading

3. **Database Queries**
   - Use indexes
   - Implement pagination
   - Cache frequently accessed data

### API Performance
1. **Caching Strategy**
   - Redis for session data
   - CDN for static assets
   - Response caching

2. **Rate Limiting**
   - 100 requests/minute per user
   - 1000 requests/hour per IP

3. **Database Optimization**
   - Connection pooling
   - Query optimization
   - Read replicas

## Security Checklist

- [ ] HTTPS everywhere
- [ ] JWT token expiration (15 minutes)
- [ ] Refresh token rotation
- [ ] Rate limiting implemented
- [ ] Input validation on all endpoints
- [ ] SQL injection prevention
- [ ] XSS protection
- [ ] CSRF protection
- [ ] Secure password hashing (bcrypt)
- [ ] Two-factor authentication
- [ ] Regular security audits
- [ ] GDPR compliance
- [ ] Data encryption at rest
- [ ] Regular backups
- [ ] Disaster recovery plan

## Cost Optimization

### Firebase (Estimated)
- Authentication: Free (50K MAU)
- Firestore: $0.18/GB storage
- Storage: $0.026/GB
- Analytics: Free

### AWS (Estimated Monthly)
- EC2 (t3.medium): $30
- RDS PostgreSQL (db.t3.medium): $70
- ElastiCache Redis: $50
- S3 Storage: $10
- CloudFront CDN: $20
- **Total: ~$180/month**

### Stripe
- 2.9% + $0.30 per transaction

### Play Store
- One-time fee: $25

### App Store
- Annual fee: $99

## Support & Maintenance

### Monitoring Tools
- Firebase Crashlytics (Crash reporting)
- Firebase Performance (Performance monitoring)
- Sentry (Error tracking)
- DataDog (Infrastructure monitoring)

### Update Strategy
1. Minor updates: Monthly
2. Major features: Quarterly
3. Security patches: As needed
4. Content updates: Weekly

### Backup Strategy
- Database: Daily automated backups
- Retention: 30 days
- Disaster recovery: 4-hour RPO

---

For questions or issues, contact: support@examplatform.com
