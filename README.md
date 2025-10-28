# Jay's Frames - Smart Scheduling & Customer Intelligence System
## Complete Implementation Guide

---

## 📋 Table of Contents
1. [System Overview](#system-overview)
2. [Installation](#installation)
3. [Configuration](#configuration)
4. [Feature Integration](#feature-integration)
5. [API Documentation](#api-documentation)
6. [Database Setup](#database-setup)
7. [Testing](#testing)
8. [Deployment](#deployment)

---

## 🎯 System Overview

This enterprise-level system provides:

### **Smart Calendar & Workload Management**
- Automatically schedules orders based on complexity, deadline, and workload
- Blocks time for complex projects
- Optimizes daily workflow
- Prevents overbooking

### **Workload-Aware Appointment Booking**
- Analyzes daily capacity before offering appointment slots
- Prioritizes lighter days for consultations
- Automatic conflict resolution
- Smart recommendations based on workload

### **Customer Referral & Rewards**
- Automated referral code generation
- Tiered reward system (Bronze, Silver, Gold, Platinum)
- Milestone rewards
- Automatic reward activation

### **Customer Behavior Intelligence**
- Predicts next purchase date and probability
- Personalized frame recommendations
- Customer segmentation
- Lifetime value calculation

### **AR Frame Preview**
- Virtual try-on for frames
- Real-time positioning and scaling
- Multiple frame style previews
- Export/share functionality

---

## 🚀 Installation

### 1. Install Dependencies

```bash
npm install
```

### 2. Set Up Environment Variables

Create `.env` file:

```env
# Database
DB_HOST=localhost
DB_USER=your_db_user
DB_PASSWORD=your_db_password
DB_NAME=jays_frames

# Server
PORT=3000
NODE_ENV=production

# JWT
JWT_SECRET=your_secret_key_here

# Email (for notifications)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASSWORD=your_email_password

# Twilio (for SMS)
TWILIO_ACCOUNT_SID=your_twilio_sid
TWILIO_AUTH_TOKEN=your_twilio_token
TWILIO_PHONE_NUMBER=+1234567890

# Discord (for Discord notifications)
DISCORD_WEBHOOK_URL=your_discord_webhook_url

# AWS S3 (for image storage)
AWS_ACCESS_KEY_ID=your_aws_key
AWS_SECRET_ACCESS_KEY=your_aws_secret
AWS_S3_BUCKET=your_bucket_name
AWS_REGION=us-east-1
```

### 3. Run Database Migrations

```bash
npm run migrate
```

### 4. Seed Initial Data (Optional)

```bash
npm run seed
```

### 5. Start the Server

```bash
# Development
npm run dev

# Production
npm start
```

---

## ⚙️ Configuration

### Smart Scheduler Configuration

```javascript
const schedulerConfig = {
  timezone: 'America/Chicago',
  workingHours: { start: '08:00', end: '17:00' },
  workingDays: [1, 2, 3, 4, 5], // Monday-Friday
  maxDailyHours: 8,
  bufferTime: 30, // minutes between tasks
  complexityThresholds: {
    simple: { maxTime: 2, priority: 3 },
    medium: { maxTime: 6, priority: 2 },
    complex: { maxTime: 24, priority: 1 }
  }
};
```

### Appointment Types Configuration

```javascript
const appointmentTypes = {
  consultation: { duration: 1, bufferTime: 15, capacity: 1 },
  pickup: { duration: 0.25, bufferTime: 5, capacity: 4 },
  delivery: { duration: 0.5, bufferTime: 10, capacity: 2 },
  frame_fitting: { duration: 0.5, bufferTime: 10, capacity: 1 },
  custom_consultation: { duration: 1.5, bufferTime: 15, capacity: 1 }
};
```

### Referral Rewards Configuration

```javascript
const rewardTiers = {
  bronze: { minReferrals: 1, bonusMultiplier: 1.0, perks: ['5% discount'] },
  silver: { minReferrals: 3, bonusMultiplier: 1.2, perks: ['10% discount', 'priority scheduling'] },
  gold: { minReferrals: 5, bonusMultiplier: 1.5, perks: ['15% discount', 'priority scheduling', 'free consultation'] },
  platinum: { minReferrals: 10, bonusMultiplier: 2.0, perks: ['20% discount', 'priority scheduling', 'free consultation', 'expedited service'] }
};

const milestoneRewards = {
  3: { type: 'credit', amount: 100, bonus: 'silver_tier' },
  5: { type: 'credit', amount: 200, bonus: 'gold_tier' },
  10: { type: 'credit', amount: 500, bonus: 'platinum_tier' },
  25: { type: 'free_service', amount: 100, bonus: 'vip_status' }
};
```

---

## 🔧 Feature Integration

### 1. Smart Calendar Integration

**Schedule a new order:**

```javascript
const orderData = {
  orderId: 'order-123',
  complexity: 'medium',
  estimatedHours: 4,
  priority: 2,
  deadline: '2025-11-15',
  dependencies: [],
  customerPreferences: {
    preferredTimeSlots: [9, 10, 11] // Morning preference
  }
};

const response = await fetch('/api/scheduling/schedule/order', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(orderData)
});

const result = await response.json();
```

**Update task progress:**

```javascript
await fetch(`/api/scheduling/schedule/task/${orderId}`, {
  method: 'PUT',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    actualHours: 3.5,
    status: 'completed'
  })
});
```

### 2. Appointment Booking Integration

**Get available slots:**

```javascript
const response = await fetch(
  `/api/scheduling/appointments/available?type=consultation&date=2025-11-01&daysAhead=14`
);
const { slots } = await response.json();

// Display slots to customer
slots.forEach(slot => {
  console.log(`${slot.formattedTime} - ${slot.workloadIndicator.text}`);
  if (slot.isRecommended) {
    console.log('⭐ Recommended time slot');
  }
});
```

**Book an appointment:**

```javascript
const appointmentData = {
  customerId: 'customer-456',
  type: 'consultation',
  datetime: '2025-11-05T10:00:00',
  duration: 1,
  notes: 'Bring artwork for framing',
  contactMethod: 'email',
  reminderPreferences: { hours: [24, 2] }
};

const response = await fetch('/api/scheduling/appointments/book', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(appointmentData)
});

const { appointmentId, confirmationNumber } = await response.json();
```

### 3. Referral System Integration

**Generate referral code:**

```javascript
const response = await fetch('/api/referrals/referral/generate', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    customerId: 'customer-456',
    customCode: null // Auto-generate
  })
});

const { code } = await response.json();
// Share code: JF8A9C2D
```

**Process referral:**

```javascript
const newCustomerData = {
  firstName: 'Jane',
  lastName: 'Smith',
  email: 'jane@example.com',
  phone: '555-0123'
};

const response = await fetch('/api/referrals/referral/process', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    referralCode: 'JF8A9C2D',
    newCustomerData
  })
});

const { referralId, newCustomerId, rewards } = await response.json();
```

**Apply reward to order:**

```javascript
await fetch('/api/referrals/reward/apply', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    customerId: 'customer-456',
    orderId: 'order-789',
    rewardId: 'reward-321'
  })
});
```

### 4. Customer Recommendations Integration

**Get personalized recommendations:**

```javascript
const response = await fetch(
  `/api/recommendations/customer/${customerId}/recommendations?artworkType=painting&size=16x20`
);

const { recommendations, basedOn } = await response.json();

recommendations.forEach(rec => {
  console.log(`${rec.frameName} - Score: ${rec.score}`);
  console.log(`Reasons: ${rec.reasons.join(', ')}`);
});
```

**Get behavior insights:**

```javascript
const response = await fetch(
  `/api/recommendations/customer/${customerId}/insights`
);

const { insights, profile } = await response.json();

insights.forEach(insight => {
  console.log(`[${insight.priority}] ${insight.message}`);
  console.log(`Action: ${insight.action}`);
});
```

### 5. AR Frame Preview Integration

**Embed in your website:**

```html
<div id="ar-preview-container"></div>

<script>
  // React component integration
  import ARFramePreview from './components/ARFramePreview';
  
  ReactDOM.render(
    <ARFramePreview />,
    document.getElementById('ar-preview-container')
  );
</script>
```

---

## 📚 API Documentation

### Authentication

All API requests require JWT authentication:

```javascript
headers: {
  'Authorization': 'Bearer YOUR_JWT_TOKEN',
  'Content-Type': 'application/json'
}
```

### Scheduling Endpoints

#### `POST /api/scheduling/schedule/order`
Schedule a new order in the production calendar.

**Request Body:**
```json
{
  "orderId": "string",
  "complexity": "simple|medium|complex",
  "estimatedHours": number,
  "priority": number,
  "deadline": "ISO8601 date",
  "dependencies": ["orderId1", "orderId2"],
  "customerPreferences": {
    "preferredTimeSlots": [9, 10, 11]
  }
}
```

**Response:**
```json
{
  "success": true,
  "task": {
    "orderId": "string",
    "startTime": "ISO8601 datetime",
    "endTime": "ISO8601 datetime",
    "complexity": number,
    "status": "scheduled"
  }
}
```

#### `GET /api/scheduling/appointments/available`
Get available appointment slots.

**Query Parameters:**
- `type`: consultation|pickup|delivery|frame_fitting|custom_consultation
- `date`: YYYY-MM-DD (optional, defaults to tomorrow)
- `daysAhead`: number (optional, defaults to 14)

**Response:**
```json
{
  "success": true,
  "slots": [
    {
      "datetime": "ISO8601 datetime",
      "formattedTime": "Monday, November 5th, 10:00 AM",
      "workloadLevel": "light|normal|heavy|overloaded",
      "workloadIndicator": {
        "color": "green",
        "text": "Light day - Great availability",
        "icon": "🟢"
      },
      "isRecommended": true,
      "recommendationScore": 95
    }
  ]
}
```

### Referral Endpoints

#### `POST /api/referrals/referral/generate`
Generate a referral code for a customer.

**Request Body:**
```json
{
  "customerId": "string",
  "customCode": "string (optional)"
}
```

#### `GET /api/referrals/customer/:id/rewards`
Get customer's available rewards.

**Query Parameters:**
- `status`: active|used|expired|pending (optional, defaults to active)

**Response:**
```json
{
  "success": true,
  "rewards": [
    {
      "id": "string",
      "type": "credit|percentage_discount|fixed_discount",
      "amount": number,
      "formattedAmount": "$25" or "10%",
      "description": "string",
      "timeUntilExpiry": "in 30 days"
    }
  ]
}
```

### Recommendation Endpoints

#### `GET /api/recommendations/customer/:id/recommendations`
Get personalized frame recommendations.

**Query Parameters:**
- `artworkType`: painting|photo|print|poster (optional)
- `size`: 8x10|16x20|24x36 (optional)

**Response:**
```json
{
  "success": true,
  "recommendations": [
    {
      "frameId": "string",
      "frameName": "Classic Black",
      "style": "modern",
      "price": 89.99,
      "score": 95.5,
      "reasons": [
        "Matches your preferred modern style",
        "Within your typical price range"
      ]
    }
  ],
  "basedOn": {
    "orderHistory": 12,
    "confidence": 85
  }
}
```

---

## 🗄️ Database Setup

### Tables Created

1. **scheduled_tasks** - Production scheduling
2. **appointments** - Customer appointments
3. **daily_workload_assessments** - Daily capacity tracking
4. **referral_codes** - Referral tracking
5. **referrals** - Referral relationships
6. **customer_rewards** - Reward management
7. **customer_behavior_profiles** - Customer intelligence
8. **frames** - Frame catalog

### Indexes

All tables include optimized indexes for:
- Fast lookups by customer ID
- Date-range queries
- Status filtering
- Analytics queries

---

## 🧪 Testing

### Run Tests

```bash
npm test
```

### Test Coverage

```bash
npm run test:coverage
```

### Sample Test Cases

```javascript
describe('Smart Scheduler', () => {
  it('should schedule order in optimal time slot', async () => {
    const result = await scheduler.scheduleOrder(sampleOrder);
    expect(result.startTime).toBeDefined();
    expect(result.conflicts).toHaveLength(0);
  });

  it('should prevent double-booking', async () => {
    await scheduler.scheduleOrder(order1);
    await expect(
      scheduler.scheduleOrder(order2WithConflict)
    ).rejects.toThrow();
  });
});
```

---

## 🚀 Deployment

### Production Checklist

- [ ] Set all environment variables
- [ ] Run database migrations
- [ ] Set up SSL certificate
- [ ] Configure CORS for your domain
- [ ] Set up monitoring (e.g., PM2, New Relic)
- [ ] Configure backups
- [ ] Set up logging (Winston)
- [ ] Rate limiting configured
- [ ] Security headers enabled (Helmet.js)

### Deploy with PM2

```bash
pm2 start server/index.js --name jays-frames-api
pm2 save
pm2 startup
```

### Docker Deployment

```dockerfile
FROM node:16-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

```bash
docker build -t jays-frames-api .
docker run -p 3000:3000 --env-file .env jays-frames-api
```

---

## 📊 Monitoring & Analytics

### Key Metrics to Track

1. **Scheduling Efficiency**
   - Average workload utilization
   - Schedule conflicts per day
   - On-time completion rate

2. **Appointment System**
   - Booking conversion rate
   - No-show rate
   - Average lead time

3. **Referral Program**
   - Referral conversion rate
   - Average reward redemption time
   - Top referrers

4. **Customer Intelligence**
   - Prediction accuracy
   - Recommendation click-through rate
   - Customer lifetime value trends

### Access Analytics Dashboard

```javascript
// Get comprehensive analytics
const analytics = {
  schedule: await fetch('/api/scheduling/analytics/schedule?startDate=2025-01-01&endDate=2025-12-31'),
  appointments: await fetch('/api/scheduling/analytics/appointments?startDate=2025-01-01&endDate=2025-12-31'),
  referrals: await fetch('/api/referrals/analytics/referrals?startDate=2025-01-01&endDate=2025-12-31')
};
```

---

## 🛠️ Maintenance

### Scheduled Tasks

Set up cron jobs for:

**Daily (6 AM):**
- Assess daily workload
- Send appointment reminders
- Expire old rewards

**Weekly:**
- Optimize schedule
- Generate analytics reports
- Clean up old data

**Monthly:**
- Update customer behavior profiles
- Calculate customer lifetime values
- Recalculate recommendation models

### Backup Strategy

```bash
# Daily database backup
0 2 * * * mysqldump -u user -p password jays_frames > backup_$(date +\%Y\%m\%d).sql

# Weekly full system backup
0 3 * * 0 tar -czf system_backup_$(date +\%Y\%m\%d).tar.gz /path/to/app
```

---

## 📞 Support & Troubleshooting

### Common Issues

**Issue: Schedule conflicts not resolving**
- Check `bufferTime` configuration
- Verify workload calculations
- Review `maxDailyHours` setting

**Issue: Referral codes not working**
- Verify code hasn't expired
- Check usage limits
- Ensure customer isn't self-referring

**Issue: Recommendations seem inaccurate**
- Verify sufficient order history (minimum 3 orders)
- Check behavior profile last update date
- Review feature weights configuration

### Logs

```bash
# View application logs
pm2 logs jays-frames-api

# View specific log file
tail -f logs/application.log
```

---

## 🎓 Best Practices

1. **Always validate user input** before processing
2. **Use transactions** for multi-step operations
3. **Cache frequently accessed data** (Redis recommended)
4. **Implement idempotency** for critical operations
5. **Monitor API response times** and optimize slow queries
6. **Regular security audits** of dependencies
7. **Keep customer data encrypted** at rest and in transit

---

## 📄 License

MIT License - See LICENSE file for details

---

## 🤝 Contributing

Contributions welcome! Please follow the contribution guidelines in CONTRIBUTING.md

---

**Built with ❤️ for Jay's Frames**
