# CI-CD-Pipeline
The Github-workflow directory contains a set of three GitHub Actions CI/CD pipeline workflows designed to automate the deployment and maintenance of a Laravel application to a remote VPS server. 

The core workflow
deploy.yml
 — is triggered automatically on every push to the master branch (or manually via the GitHub UI). It connects to the VPS over SSH using the appleboy/ssh-action, then executes a structured 5-step deployment script: it checks container health, performs a MySQL database backup before any changes are made, pulls the latest code from the master branch using git reset --hard, restarts the Docker containers, and runs Laravel post-deployment tasks including composer install, php artisan migrate, php artisan optimize, and folder permission setup. After deployment, it waits 20 seconds and hits http://localhost:8787 to confirm the application is live, then performs a final remote verification with up to 3 retry attempts from the GitHub runner itself.

The directory also includes two supporting workflows for safety and diagnostics. 

The rollback.yml
workflow is a manually triggered emergency rollback that accepts a backup filename as input and SSHs into the VPS to execute a restore.sh script — allowing the team to quickly revert the database to a previous state in case a bad deployment is pushed. 

The verify-secrets.yml
workflow is a lightweight secrets audit tool that can be run on-demand to confirm that all three required GitHub Secrets
(VPS_HOST, VPS_USER, VPS_SSH_PRIVATE_KEY) are properly configured in the repository before a real deployment is attempted — making it a useful onboarding and troubleshooting utility for the team.
