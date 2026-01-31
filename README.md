# 1. Fetch local env vars
```
export $(cat .env | grep -v '^#' | xargs)
```

# 2. Authenticate Docker to ECR (using your .env values!)
aws ecr get-login-password --region $DEFAULT_AWS_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$DEFAULT_AWS_REGION.amazonaws.com

# 3. Build for Linux/AMD64 (CRITICAL for Apple Silicon Macs!)
docker build \
  --platform linux/amd64 \
  --build-arg NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY="$NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY" \
  -t consultation-app .

# 4. Tag your image (using your .env values!)
docker tag consultation-app:latest $AWS_ACCOUNT_ID.dkr.ecr.$DEFAULT_AWS_REGION.amazonaws.com/consultation-app:latest

# 5. Push to ECR
docker push $AWS_ACCOUNT_ID.dkr.ecr.$DEFAULT_AWS_REGION.amazonaws.com/consultation-app:latest


# 6. Run locally
```
docker run -p 8000:8000 \
  -e CLERK_SECRET_KEY="$CLERK_SECRET_KEY" \
  -e CLERK_JWKS_URL="$CLERK_JWKS_URL" \
  -e OPENAI_API_KEY="$OPENAI_API_KEY" \
  consultation-app
```