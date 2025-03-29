# Kubernetes Deployment for Flask Application

This repository demonstrates the end-to-end deployment of a Flask web application on a Kubernetes cluster (using Minikube). It covers containerizing the application, creating Kubernetes resources, implementing auto-scaling with HPA, rolling updates/rollbacks, logging, and self-healing tests using a load generator.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Setup and Deployment](#setup-and-deployment)
  - [1. Start Kubernetes Cluster](#1-start-kubernetes-cluster)
  - [2. Build and Push the Docker Image](#2-build-and-push-the-docker-image)
  - [3. Create Kubernetes Resources](#3-create-kubernetes-resources)
    - [Deployment](#deployment)
    - [Service](#service)
    - [ConfigMap](#configmap)
  - [4. Implement Auto-Scaling (HPA)](#4-implement-auto-scaling-hpa)
  - [5. Rolling Updates & Rollbacks](#5-rolling-updates--rollbacks)
  - [6. Logging](#6-logging)
  - [7. Self-Healing & Load Generation Tests](#7-self-healing--load-generation-tests)
- [Testing Scenarios](#testing-scenarios)
- [Notes](#notes)

---

## Prerequisites

- **Minikube** installed and running.
- **kubectl** configured for the Minikube cluster.
- **Docker** installed for building images.
- A Docker Hub account (repository: `garvitpathak27/hello-world-flask`).

---

## Project Structure

