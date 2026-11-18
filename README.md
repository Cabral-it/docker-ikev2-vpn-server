# docker-ikev2-vpn-server

Run an IKEv2 VPN server in Docker and generate a connection profile for iOS or macOS. The server creates a shared secret when the container is created; clients use that secret rather than certificates or username/password credentials.

## Features

- Run the VPN server in a Docker container with an automatic restart policy.
- Expose IKEv2 traffic through UDP ports 500 and 4500.
- Generate an Apple `.mobileconfig` profile for a specified hostname or IP address.
- Create a shared secret for client authentication at container creation.

## Installation

Install Docker on the server and confirm that you can run it:

```sh
docker --version
```

The commands below use the donor project's published image, `gaomd/ikev2-vpn-server:0.3.0`. They do not assume a separate image has been published for this standalone project.

```sh
docker pull gaomd/ikev2-vpn-server:0.3.0
```

Allow inbound UDP traffic on ports 500 and 4500 in the server firewall and any applicable cloud firewall. If the server is behind a router, forward those ports to it.

**The container runs with `--privileged`.** Review that level of host access before you deploy it.

## Usage

Start the VPN server:

```sh
docker run --privileged -d --name ikev2-vpn-server --restart=always -p 500:500/udp -p 4500:4500/udp gaomd/ikev2-vpn-server:0.3.0
```

Generate a client profile with the running container's shared secret:

```sh
docker run --privileged -i -t --rm --volumes-from ikev2-vpn-server -e "HOST=vpn1.example.com" gaomd/ikev2-vpn-server:0.3.0 generate-mobileconfig > ikev2-vpn.mobileconfig
```

Replace `vpn1.example.com` with a domain name that resolves to your server's public IP address. A public IP address can also be used directly.

**Treat the generated profile as a credential.** Transfer `ikev2-vpn.mobileconfig` to your device with `scp`, AirDrop, or another secure method.

On iOS, open the transferred profile and complete profile installation in Settings. On macOS, open the file and follow the profile installation prompts. The exact steps depend on the operating system version.

The donor documentation lists iOS 8 or later and macOS 10.11 or later as protocol requirements. It does not establish compatibility with every current release.

## License

MIT. This project is based on [gaomd/docker-ikev2-vpn-server](https://github.com/gaomd/docker-ikev2-vpn-server).

Original work copyright (c) 2016 Mengdi Gao. Preserve the original copyright and MIT permission notice in copies or substantial portions of the software. See [LICENSE](LICENSE) for the full terms.
