# nginx-ecr-pipeline

A small CI/CD project. Every push to `main` builds a Docker image (nginx serving one web page) and pushes it to a private Amazon ECR repo.

## How it works

1. I push code to GitHub.
2. GitHub Actions starts (`.github/workflows/build-push.yml`).
3. The robot logs in to AWS with OIDC, so no passwords or keys are saved anywhere.
4. It builds the image from the `Dockerfile`.
5. It pushes the image to the ECR repo `my-nginx` in `ap-south-1`, tagged with the commit ID.

## Files

- `Dockerfile`: the recipe for the image
- `index.html`: the web page
- `.github/workflows/build-push.yml`: the robot's to-do list

## Tools used

GitHub Actions, Docker, nginx, AWS IAM (OIDC role), Amazon ECR
