# imagine.lua

Tarantool framework.

- Provides convenient setup of an access control.
- Configures monitoring with Graphite.

## Usage

To create ready to go version of imagine.lua and Grafana dashboard run:
```sh
make own
```

## Example

```lua
local function users_create(login)
    box.space.users:insert({login, fiber.time()})
end

local function users_list()
    return box.space.users:select({})
end

local function init()
    box.schema.create_space('users', {if_not_exists = true})
    box.space.users:create_index('pk', {type = 'hash', parts = {1, 'str'}, if_not_exists = true})
end

imagine.init({
    init_func = init,

    roles = {
        backend_role = {
            table = 'users',
            funcs = {
                create = imagine.atomic(users_create),
                list   = imagine.atomic(users_list),
            },
        },
    },
})

```

[Full example](example/)
