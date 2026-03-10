# AWS Web Application

A simple Node.js web application with Docker containerization, ready for deployment on AWS.

## Features

- Express.js web server
- CORS enabled
- Health check endpoint
- Docker containerized
- AWS-ready deployment
- Multi-stage Docker build for optimized images

## Local Development

### Prerequisites
- Node.js 18.x
- npm or yarn
- Docker (for containerized deployment)

### Installation

```bash
npm install
```

### Running Locally

```bash
npm start
```

Visit `http://localhost:3000` in your browser.

### Development Mode

```bash
npm run dev
```

## Docker

### Build Docker Image

```bash
docker build -t aws-web-app:latest .
```

### Run Docker Container Locally

```bash
docker run -p 3000:3000 -e NODE_ENV=production aws-web-app:latest
```

## AWS Deployment

### Option 1: Amazon ECS (Recommended)

1. **Create ECR Repository**
   ```bash
   aws ecr create-repository --repository-name aws-web-app --region us-east-1
   ```

2. **Build and Push to ECR**
   ```bash
   # Get ECR login token
   aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <YOUR_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com

   # Build and tag image
   docker build -t aws-web-app:latest .
   docker tag aws-web-app:latest <YOUR_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/aws-web-app:latest

   # Push to ECR
   docker push <YOUR_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/aws-web-app:latest
   ```

3. **Create ECS Cluster, Task Definition, and Service**
   - Use AWS Console or AWS CLI to create an ECS cluster
   - Create a task definition pointing to your ECR image
   - Create a service with load balancer (ALB) for high availability

### Option 2: AWS Elastic Beanstalk

1. **Install Elastic Beanstalk CLI**
   ```bash
   pip install awsebcli
   ```

2. **Initialize and Deploy**
   ```bash
   eb init -p "Docker running on 64bit Amazon Linux 2"
   eb create aws-web-app-env
   eb deploy
   ```

### Option 3: AWS App Runner

1. **Connect your GitHub repository**
2. **Configure App Runner with:**
   - Source: GitHub repo
   - Build command: `npm install`
   - Start command: `node app.js`
   - Port: 3000

## API Endpoints

- **GET** `/` - Welcome message
- **GET** `/api/health` - Health check
- **GET** `/api/info` - Application information

## Environment Variables

- `PORT` - Server port (default: 3000)
- `NODE_ENV` - Environment (development/production)

## Testing

```bash
curl http://localhost:3000
curl http://localhost:3000/api/health
curl http://localhost:3000/api/info
```

## Security Considerations

- Use Alpine Linux for smaller attack surface
- Run container as non-root user (recommended for production)
- Use AWS Secrets Manager for sensitive data
- Enable VPC security groups and NACLs
- Use HTTPS/TLS with ALB

## Monitoring

- Configure CloudWatch logs in ECS task definition
- Set up CloudWatch alarms for health check failures
- Use X-Ray for distributed tracing

## License

MIT