.PHONY: run-all run-auth run-user run-order run-product stop-all stop-auth stop-user stop-order stop-product remove-all remove-auth remove-user remove-order remove-product create-network help

NETWORK_NAME=microservices-network
CREATE_NETWORK?=yes

help:
	@echo "Makefile for running microservices"
	@echo ""
	@echo "RUN COMMANDS:"
	@echo "  make run-all                    - Run all microservices with network"
	@echo "  make run-all CREATE_NETWORK=no  - Run all services without network"
	@echo "  make run-auth                   - Run auth-service on port 5000"
	@echo "  make run-user                   - Run user-service on port 5001"
	@echo "  make run-order                  - Run order-service on port 5002"
	@echo "  make run-product                - Run product-service on port 5003"
	@echo ""
	@echo "STOP COMMANDS:"
	@echo "  make stop-all                   - Stop all running containers"
	@echo "  make stop-auth                  - Stop auth-service"
	@echo "  make stop-user                  - Stop user-service"
	@echo "  make stop-order                 - Stop order-service"
	@echo "  make stop-product               - Stop product-service"
	@echo ""
	@echo "REMOVE COMMANDS:"
	@echo "  make remove-all                 - Remove all containers"
	@echo "  make remove-auth                - Remove auth-service container"
	@echo "  make remove-user                - Remove user-service container"
	@echo "  make remove-order               - Remove order-service container"
	@echo "  make remove-product             - Remove product-service container"
	@echo ""
	@echo "NETWORK COMMANDS:"
	@echo "  make create-network             - Create Docker network"
	@echo ""

create-network:
	docker network create $(NETWORK_NAME) 2>/dev/null || echo "Network $(NETWORK_NAME) already exists"
	@echo "Network $(NETWORK_NAME) ready"

run-all:
ifeq ($(CREATE_NETWORK),yes)
	@$(MAKE) create-network
endif
	@$(MAKE) run-auth run-user run-order run-product
	@echo "All microservices are running"

run-auth:
ifeq ($(CREATE_NETWORK),yes)
	docker run -d -p 5000:5000 --network $(NETWORK_NAME) --env-file auth-service/.env auth-service:1.0
else
	docker run -d -p 5000:5000 --env-file auth-service/.env auth-service:1.0
endif

run-user:
ifeq ($(CREATE_NETWORK),yes)
	docker run -d -p 5001:5001 --network $(NETWORK_NAME) --env-file user-service/.env user-service:1.0
else
	docker run -d -p 5001:5001 --env-file user-service/.env user-service:1.0
endif

run-order:
ifeq ($(CREATE_NETWORK),yes)
	docker run -d -p 5002:5002 --network $(NETWORK_NAME) --env-file order-service/.env order-service:1.0
else
	docker run -d -p 5002:5002 --env-file order-service/.env order-service:1.0
endif

run-product:
ifeq ($(CREATE_NETWORK),yes)
	docker run -d -p 5003:5003 --network $(NETWORK_NAME) --env-file product-service/.env product-service:1.0
else
	docker run -d -p 5003:5003 --env-file product-service/.env product-service:1.0
endif

stop-all:
	docker stop $$(docker ps -q --filter "ancestor=auth-service:1.0" --filter "ancestor=user-service:1.0" --filter "ancestor=order-service:1.0" --filter "ancestor=product-service:1.0") 2>/dev/null || true
	@echo "All microservices stopped"

stop-auth:
	docker stop $$(docker ps -q --filter "ancestor=auth-service:1.0") 2>/dev/null || true
	@echo "auth-service stopped"

stop-user:
	docker stop $$(docker ps -q --filter "ancestor=user-service:1.0") 2>/dev/null || true
	@echo "user-service stopped"

stop-order:
	docker stop $$(docker ps -q --filter "ancestor=order-service:1.0") 2>/dev/null || true
	@echo "order-service stopped"

stop-product:
	docker stop $$(docker ps -q --filter "ancestor=product-service:1.0") 2>/dev/null || true
	@echo "product-service stopped"

remove-all: stop-all
	docker rm $$(docker ps -a -q --filter "ancestor=auth-service:1.0" --filter "ancestor=user-service:1.0" --filter "ancestor=order-service:1.0" --filter "ancestor=product-service:1.0") 2>/dev/null || true
	@echo "All containers removed"

remove-auth: stop-auth
	docker rm $$(docker ps -a -q --filter "ancestor=auth-service:1.0") 2>/dev/null || true
	@echo "auth-service container removed"

remove-user: stop-user
	docker rm $$(docker ps -a -q --filter "ancestor=user-service:1.0") 2>/dev/null || true
	@echo "user-service container removed"

remove-order: stop-order
	docker rm $$(docker ps -a -q --filter "ancestor=order-service:1.0") 2>/dev/null || true
	@echo "order-service container removed"

remove-product: stop-product
	docker rm $$(docker ps -a -q --filter "ancestor=product-service:1.0") 2>/dev/null || true
	@echo "product-service container removed"
