# deerflow-vm

Bootstrapping [DeerFlow](https://deerflow.tech/).

What is DeerFlow?

> An open-source long-horizon SuperAgent harness that researches, codes, and creates. With the help of sandboxes, memories, tools, skills and subagents, it handles different levels of tasks that could take minutes to hours.

## All-In-One

The supplied Vagrantfile will (fully) provision a Virtualbox VM with a running DeerFlow instance.

Once started, you can access the web interface on your host browser at http://localhost:2026.

### VM Specs

* Number of CPUs: 4
* Memory: 8 GB
* Disk: 50 GB (15 GB will be used initially)

### Software

The guest VM runs Ubuntu 26.04 LTS.

Dependencies installed include:

* Docker Engine and BuildKit
* node.js 22
* pnpm 10
* Astral uv and Python 3

The latest version of [DeerFlow](https://github.com/bytedance/deer-flow) is cloned.

Last, set-up a systemd service to spin up DeerFlow on system boot and restart gracefully if something crashes.

### References

* [DeerFlow Documentation](https://deerflow.tech/en/docs)

