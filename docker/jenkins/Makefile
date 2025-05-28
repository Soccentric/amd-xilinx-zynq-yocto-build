# Makefile for Docker and Docker Compose commands with Jenkins support

# Use host user/group IDs for container permissions (inherited from parent Makefile or set here)
USER_ID ?= $(shell id -u)
GROUP_ID ?= $(shell id -g)

.PHONY: build up down ps logs shell clean jenkins-password jenkins-status jenkins-restart jenkins-logs jenkins-cli

# Build the Docker image
build:
	USER_ID=$(USER_ID) GROUP_ID=$(GROUP_ID) docker-compose build

# Start containers
up:
	USER_ID=$(USER_ID) GROUP_ID=$(GROUP_ID) docker-compose up -d

# Stop containers
down:
	docker-compose down

# Show running containers
ps:
	docker-compose ps

# Show container logs
logs:
	docker-compose logs

# Open a shell in the rpi container
shell:
	docker-compose exec rpi bash

# Clean up Docker resources
clean:
	docker-compose down --rmi all --volumes --remove-orphans

# Jenkins commands

# Get Jenkins initial admin password
jenkins-password:
	docker-compose exec rpi cat /var/jenkins_home/secrets/initialAdminPassword

# Check Jenkins service status
jenkins-status:
	docker-compose exec rpi service jenkins status

# Restart Jenkins service
jenkins-restart:
	docker-compose exec rpi service jenkins restart

# Show Jenkins logs
jenkins-logs:
	docker-compose exec rpi tail -f /var/jenkins_home/jenkins.log

# Run Jenkins CLI command (usage: make jenkins-cli cmd="help")
jenkins-cli:
	@if [ -z "$(cmd)" ]; then \
		echo "Usage: make jenkins-cli cmd=\"command\""; \
	else \
		docker-compose exec rpi java -jar /var/jenkins_home/war/WEB-INF/jenkins-cli.jar -s http://localhost:8080/ -auth admin:$(shell cat .jenkins-token 2>/dev/null || echo "admin") $(cmd); \
	fi

# Help command
help:
	@echo "Available commands:"
	@echo "  make build            - Build Docker image"
	@echo "  make up               - Start containers"
	@echo "  make down             - Stop containers"
	@echo "  make ps               - List running containers"
	@echo "  make logs             - Show container logs"
	@echo "  make shell            - Open shell in rpi container"
	@echo "  make clean            - Clean up Docker resources"
	@echo "  make jenkins-password - Get Jenkins initial admin password"
	@echo "  make jenkins-status   - Check Jenkins service status"
	@echo "  make jenkins-restart  - Restart Jenkins service"
	@echo "  make jenkins-logs     - Show Jenkins logs"
	@echo "  make jenkins-cli      - Run Jenkins CLI commands (usage: make jenkins-cli cmd=\"help\")"
