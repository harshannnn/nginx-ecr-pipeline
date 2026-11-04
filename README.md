# nginx-ecr-pipeline

A small CI/CD pipeline: every push builds a Docker image, tests it, pushes it
to Amazon ECR and deploys it to an EC2 instance, with no saved AWS keys.

## What happens on every push

1. **Log in to AWS** with GitHub OIDC (a short-lived role, no access keys stored)
2. **Build** the Docker image, tagged with the commit ID
3. **Test** it: start the container and check it answers with `curl`
4. **Push** the image to a private ECR repository (`my-nginx`, ap-south-1)
5. **Deploy** to EC2 through AWS Systems Manager: log in to ECR, pull the
   exact commit tag, replace the old container, run the new one on port 8080

If the test fails, nothing is pushed. If the deploy fails, the run turns red.
README-only changes do not start the pipeline.

## Why I built it this way

- **OIDC instead of access keys:** nothing secret is stored in GitHub except a role ARN.
- **Commit-ID tag instead of `latest`:** the server always runs exactly what the
  pipeline just built, and every image can be traced to a commit.
- **SSM instead of SSH:** no open login keys; the pipeline only has permission
  to run commands on one instance.
- **Least privilege:** the EC2 role can only pull from ECR, and the pipeline
  role can only send commands to this instance.

## Tech used

GitHub Actions, Docker, Amazon ECR, EC2, IAM roles, AWS Systems Manager, nginx
