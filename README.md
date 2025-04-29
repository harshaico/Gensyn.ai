# Gensyn.ai
Gensyn.ai
Got it — you want a script to run a Gensyn.ai testnet node inside WSL2 (Windows Subsystem for Linux 2).

I'll make you a simple, ready-to-run bash script that:

Installs dependencies (Docker, etc.)
Sets up environment variables
Pulls the Gensyn.ai testnet node (assuming it's public or available by instructions)
Runs it properly inside WSL2.
Here’s a basic setup + run script:

#!/bin/bash

# Script: setup-gensyn-node.sh
# Purpose: Install Docker, pull Gensyn testnet node, and run it in WSL2

set -e

echo "Updating packages..."
sudo apt update && sudo apt upgrade -y

echo "Installing dependencies..."
sudo apt install -y ca-certificates curl gnupg lsb-release

echo "Setting up Docker repository..."
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
    sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

echo "Installing Docker Engine..."
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

echo "Adding current user to docker group..."
sudo usermod -aG docker $USER

echo "Docker installation complete. You might need to restart WSL after this script."

# --- Gensyn Specific Part ---
echo "Pulling Gensyn testnet node..."

# Assuming there's a public docker image (replace below if different)
GENSYN_IMAGE="gensynai/testnet-node:latest"

docker pull $GENSYN_IMAGE

echo "Running Gensyn node container..."
docker run -d \
  --name gensyn-testnet-node \
  -p 30333:30333 \          # Example: expose p2p port
  -p 9933:9933 \            # Example: expose RPC port
  -v gensyn-data:/root/.gensyn \   # Persist node data
  $GENSYN_IMAGE

echo "Gensyn node is now running!"

echo "Check logs anytime with: docker logs -f gensyn-testnet-node"
Quick Instructions:
Save it as setup-gensyn-node.sh
Run:
chmod +x setup-gensyn-node.sh
./setup-gensyn-node.sh
If Docker group changes don’t apply, restart WSL2:
wsl --shutdown
Assumptions:
You’re using Ubuntu inside WSL2.
Gensyn has a Docker image for their node (otherwise I can make a non-Docker version too).
Ports 30333 and 9933 are examples — replace them if Gensyn uses different ones.
You want persistent storage using a named volume gensyn-data.
⚡ Bonus: "Auto-start on boot" (Optional)
If you want your node to auto-start every time WSL2 starts:

echo "docker start gensyn-testnet-node" >> ~/.bashrc
Would you like me to also make a docker-compose.yml version?
It would be even cleaner and easier to maintain! 🚀
(Just say "yes" if you want that!)
