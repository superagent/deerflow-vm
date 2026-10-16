Vagrant.configure("2") do |config|
  # Use the official Ubuntu 26.04 LTS box (or standard cloud image placeholder)
  config.vm.box = "cloud-image/ubuntu-26.04"

  # Enable synced folder (one-way sync from host to guest)
  config.vm.synced_folder ".", "/vagrant", SharedFoldersEnableSymlinksCreate: false, type: "rsync"

  # Forward the required DeerFlow ports to your host machine
  # 2026: Nginx unified entry URL
  config.vm.network "forwarded_port", guest: 2026, host: 2026, auto_correct: true

  # Resource Allocation (DeerFlow 2.0 recommends a baseline of at least 8GB RAM / 4 vCPUs)
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "8192"
    vb.cpus = 4
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  end

  # Expand the primary storage drive (default: 10GB is too small)
  config.vm.disk :disk, size: "50GB", primary: true

  # Provisioning Shell Script
  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    echo "=== Change vagrant user login shell ==="
    sudo chsh -s /bin/bash vagrant

    echo "=== Updating System Packages ==="
    sudo DEBIAN_FRONTEND=noninteractive apt-get update && sudo DEBIAN_FRONTEND=noninteractive apt-get upgrade -y

    echo "=== Installing Essential Tools & Nginx ==="
    sudo DEBIAN_FRONTEND=noninteractive apt-get install -y git curl wget build-essential nginx jq

    echo "=== Setting up modern keyring directory and external repositories ==="
    sudo DEBIAN_FRONTEND=noninteractive apt-get install -y ca-certificates curl gnupg
    sudo mkdir -p /etc/apt/keyrings

    # Download and de-armor NodeSource GPG signing key
    curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
    # Create the direct repository sources list entry for Node.js 22
    echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_22.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list

    sudo DEBIAN_FRONTEND=noninteractive apt-get update

    echo "=== Installing Docker Engine and (optional) BuildkKit ==="
    sudo DEBIAN_FRONTEND=noninteractive apt-get install -y docker.io docker-compose-v2 docker-buildx
    sudo usermod -aG docker vagrant

    # DeerFlow expects pnpm 10; ignore the notice that pnpm 12 is available
    echo "=== Installing Node.js 22 & pnpm ==="
    sudo DEBIAN_FRONTEND=noninteractive apt-get install -y nodejs
    curl -fsSL https://get.pnpm.io | SHELL=/bin/bash sh -

    echo "=== Installing Python & Astral uv ==="
    sudo DEBIAN_FRONTEND=noninteractive apt-get install -y python3 python3-venv python3-pip
    curl -fsSL https://astral.sh/uv/install.sh | sh -

    source ~/.local/bin/env

    echo "=== Cloning the deer-flow repository ==="
    git clone https://github.com/bytedance/deer-flow.git
    cd deer-flow
    sed -i 's/BIND_HOST:-127.0.0.1/BIND_HOST:-0.0.0.0/' docker/docker-compose-dev.yaml

    echo "=== Initializing the local configuration ==="
    make config
    make install

    echo "=== Provisioning Complete! ==="
    echo "Please run 'vagrant ssh' to jump into your environment."
    echo "Choose your sandbox provide and add at least one model to 'config.yaml'."
    echo "Run 'make dev' to start all services."
  SHELL
end
