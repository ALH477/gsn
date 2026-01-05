# DeMoD Game Server Network - Integrated DCF Stack

This repository contains the integrated Docker Compose configuration for the DeMoD Game Server Network (GSN). The stack provides a complete ecosystem for game server orchestration, traffic metering, and automated billing through DeMoD Cloud Fabric (DCF) integration.

## Overview

The GSN stack consists of several interconnected services working together to provide a seamless game server hosting experience:

* 
**Discord Bot**: A lightweight command interface for users to manage their game servers directly from Discord.


* 
**Game Selector**: The orchestration engine that handles the lifecycle of game server containers while verifying authentication through DCF-ID.


* 
**DCF-ID**: The central identity and billing service that manages user accounts, Stripe integration, and authentication.


* 
**GSN Meter**: A high-performance proxy that captures and reports game traffic usage for real-time billing.


* 
**Game Servers**: Dynamically spawned server containers (e.g., Minecraft) that route traffic through the GSN Meter.



## Prerequisites

Before deploying the stack, ensure you have the following:

* 
**Docker and Docker Compose**: Installed on your host system.


* 
**External Network**: A pre-existing Docker network named `frontend`.


* 
**External Volume**: A pre-existing Docker volume named `demod-data`.


* 
**Stripe Account**: Required for billing integration.


* 
**Discord Application**: Required for bot and OAuth2 integration.



## Deployment

### 1. Build and Load Images

If you are using the Nix-based workflow, build the Docker images and load them into your local Docker daemon:

```bash
nix build .#docker-gsn-selector && ./result | docker load
nix build .#docker-gsn-meter && ./result | docker load
nix build .#docker-gsn-discord-bot && ./result | docker load
nix build .#docker-dcf-id && ./result | docker load

```



### 2. Configure Environment Variables

Create a `.env` file in the same directory as `docker-compose.yml` and populate it with your specific secrets:

```env
# Service Secrets
DCF_ID_INTERNAL_KEY=your_secret_internal_key
STRIPE_SECRET_KEY=sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...

# Discord Integration
DISCORD_CLIENT_ID=your_client_id
DISCORD_CLIENT_SECRET=your_client_secret
DISCORD_TOKEN=your_bot_token

# Configuration
DCF_PUBLIC_URL=https://dcf.demod.ltd
LOG_LEVEL=info

```



### 3. Launch the Stack

Start the services in detached mode:

```bash
docker compose up -d

```



## Network and Security

* 
**Isolation**: Internal communication occurs over the `dcf-bridge` network (`172.30.0.0/16`).


* 
**Hardening**: Services are configured with `no-new-privileges:true` and specific resource limits to ensure system stability.


* 
**Traffic Routing**: Game traffic enters via the `gsn-meter` on UDP port 9000 (and subsequent ports for additional servers).



---

## License

Copyright (c) 2025, DeMoD LLC. All rights reserved.

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.
2. Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.
3. Neither the name of DeMoD LLC nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
