# Drone CI test system

### Prerequisites

- Vagrant with a provider enabled (tested with vagrant-libvirt, but this should work on stock VirtualBox as well)
- bento/debian-10 VM image (Vagrant will download this for you if needed)

### Quick Start

Go to `scm-server`, read and modify the `Vagrantfile` as needed, and start the Gitea SCM:

```sh
cd scm-server
vim Vagrantfile
vagrant up
```

Take note of the `Gitea URL` once Vagrant finishes provisioning - visit it and finish the installation in its VM:

- Use `SQLite` for its database
- Set `Gitea Base URL` to use the same base as the above `Gitea URL`
- Set `SSH Server Domain` with the same as above
- Create a first user that will automatically be designated as Gitea admin

Once this first user is created and logged in, go to `Settings -> Applications` and add a new OAuth2 application as described in https://docs.drone.io/server/provider/gitea/

- Set the `Redirect URI` to something random for now, as this will be updated later

After adding this, a new `Client ID` and `Client Secret` should be generated - take note of these for the next step.

Go to `drone-server-runner`, and copy the `dotenv.example` into `dotenv`:

```sh
cd ../drone-server-runner
cp dotenv.example dotenv
vim dotenv
```

Edit `dotenv` and update to use the relevant information needed for the Drone server to connect to the Gitea SCM:

- `DRONE_GITEA_SERVER`
- `DRONE_GITEA_CLIENT_ID`
- `DRONE_GITEA_CLIENT_SECRET`
- `DRONE_USER_CREATE`

Then read/modify its `Vagrantfile` as needed, and start up the Drone VM, anticipating its `Drone Server UI` URL!

```sh
vagrant up
```

After the Drone VM converges, update the Gitea OAuth2 Redirect URI we set earlier to use the Drone URL above.

### Testing

- Create new repositories in Gitea and add code into it
- Activate repositories in Drone Server UI and trigger builds from it
- Update repositories and/or create new PRs in Gitea then see Drone picking these up for triggering builds
- Install `drone` CLI and explore using it against the Drone Server API and for doing local builds on repositories
- Update Drone Server/Runner configuration by changing `docker-compose.yml` directly and restarting the services
