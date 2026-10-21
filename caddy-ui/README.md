# caddy/ui for Unraid

[caddy/ui](https://github.com/zackwag/caddy-ui) requires both templates in this directory and an existing Caddy server. The frontend serves the UI; the backend manages Caddy through its admin API and Docker socket.

1. In the Unraid terminal, create a user-defined bridge network: `docker network create caddy-ui`. Skip this if that network already exists.
2. Select `Custom: caddy-ui` as the network for both UI containers and your Caddy container. If you already use a user-defined bridge network for Caddy, you can select that network for both UI containers instead. Keep their names `caddy-ui-backend` and `caddy-ui-frontend` so Docker DNS and frontend proxy discovery work.
3. Add `admin 0.0.0.0:2019` to the existing global block in your Caddyfile (or create a global block if absent), then reload Caddy. Keep port 2019 internal to the Docker network; do not publish it to the internet.
4. Install and start the backend. Set **External UI URL** to the exact address you will open, such as `http://192.168.1.10:9877`. For an HTTPS reverse proxy, use the external HTTPS URL instead. The template leaves this required field empty because your server address is installation-specific.
5. Install and start the frontend. Open its WebUI on port **9877**, create the administrator, and save the recovery key. Keep access restricted to yourself until enrollment is complete.
6. Discover or add your Caddy instance in the UI. On the shared network, a container named `caddy` has the admin URL `http://caddy:2019`.

The backend persists credentials, settings, instances, and history in `/mnt/user/appdata/caddy-ui`. Back up this directory. Caddy's own data stays in its existing volumes; those volumes do not need additional backend mounts. The Docker socket enables container discovery and file operations and grants control of the Docker host.

Only the frontend publishes a host port. The backend listens internally on port 3001. When changing the frontend's host port or hostname, update **External UI URL** on the backend too. If running behind another reverse proxy, configure **Trusted upstream proxies** on the frontend with that proxy's actual source IP or narrow CIDR to preserve client addresses for rate limiting.

The templates use upstream's `latest` stable images. Update both together. These images do not document PUID, PGID, or UMASK support, so the templates retain their upstream runtime users.

The frontend's `logo.png` is the upstream `src/frontend/caddyui-logo.png`, distributed under the project's [MIT license](https://github.com/zackwag/caddy-ui/blob/main/LICENSE). The backend uses `logo-backend.png`, an edited variant with a database badge in the bottom-right corner. The upstream license notice is retained in `LICENSE.logo`.
