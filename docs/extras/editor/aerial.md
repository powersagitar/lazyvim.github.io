# Aerial

<!-- plugins:start -->

:::info
You can enable the extra with the `:LazyExtras` command.
Plugins marked as optional will only be configured if they are installed.
:::

Below you can find a list of included plugins and their default settings.

:::caution
You don't need to copy the default settings to your config.
They are only shown here for reference.
:::

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## [aerial.nvim](https://github.com/stevearc/aerial.nvim)

<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = function()
  local icons = vim.deepcopy(LazyVim.config.icons.kinds)

  -- HACK: fix lua's weird choice for `Package` for control
  -- structures like if/else/for/etc.
  icons.lua = { Package = icons.Control }

  ---@type table<string, string[]>|false
  local filter_kind = false
  if LazyVim.config.kind_filter then
    filter_kind = assert(vim.deepcopy(LazyVim.config.kind_filter))
    filter_kind._ = filter_kind.default
    filter_kind.default = nil
  end

  local opts = {
    attach_mode = "global",
    backends = { "lsp", "treesitter", "markdown", "man" },
    show_guides = true,
    layout = {
      resize_to_content = false,
      win_opts = {
        winhl = "Normal:NormalFloat,FloatBorder:NormalFloat,SignColumn:SignColumnSB",
        signcolumn = "yes",
        statuscolumn = " ",
      },
    },
    icons = icons,
    filter_kind = filter_kind,
    -- stylua: ignore
    guides = {
      mid_item   = "├╴",
      last_item  = "└╴",
      nested_top = "│ ",
      whitespace = "  ",
    },
  }
  return opts
end
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "stevearc/aerial.nvim",
  event = "LazyFile",
  opts = function()
    local icons = vim.deepcopy(LazyVim.config.icons.kinds)

    -- HACK: fix lua's weird choice for `Package` for control
    -- structures like if/else/for/etc.
    icons.lua = { Package = icons.Control }

    ---@type table<string, string[]>|false
    local filter_kind = false
    if LazyVim.config.kind_filter then
      filter_kind = assert(vim.deepcopy(LazyVim.config.kind_filter))
      filter_kind._ = filter_kind.default
      filter_kind.default = nil
    end

    local opts = {
      attach_mode = "global",
      backends = { "lsp", "treesitter", "markdown", "man" },
      show_guides = true,
      layout = {
        resize_to_content = false,
        win_opts = {
          winhl = "Normal:NormalFloat,FloatBorder:NormalFloat,SignColumn:SignColumnSB",
          signcolumn = "yes",
          statuscolumn = " ",
        },
      },
      icons = icons,
      filter_kind = filter_kind,
      -- stylua: ignore
      guides = {
        mid_item   = "├╴",
        last_item  = "└╴",
        nested_top = "│ ",
        whitespace = "  ",
      },
    }
    return opts
  end,
  keys = {
    { "<leader>cs", "<cmd>AerialToggle<cr>", desc = "Aerial (Symbols)" },
  },
}
```

</TabItem>

</Tabs>

## [trouble.nvim](https://github.com/folke/trouble.nvim) _(optional)_

<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = nil
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "folke/trouble.nvim",
  optional = true,
  keys = {
    { "<leader>cs", false },
  },
}
```

</TabItem>

</Tabs>

## [telescope.nvim](https://github.com/nvim-telescope/telescope.nvim) _(optional)_

 Telescope integration


<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = function()
  LazyVim.on_load("telescope.nvim", function()
    require("telescope").load_extension("aerial")
  end)
end
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "nvim-telescope/telescope.nvim",
  optional = true,
  opts = function()
    LazyVim.on_load("telescope.nvim", function()
      require("telescope").load_extension("aerial")
    end)
  end,
  keys = {
    {
      "<leader>ss",
      "<cmd>Telescope aerial<cr>",
      desc = "Goto Symbol (Aerial)",
    },
  },
}
```

</TabItem>

</Tabs>

## [edgy.nvim](https://github.com/folke/edgy.nvim) _(optional)_

 edgy integration


<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = function(_, opts)
  local edgy_idx = LazyVim.plugin.extra_idx("ui.edgy")
  local aerial_idx = LazyVim.plugin.extra_idx("editor.aerial")

  if edgy_idx and edgy_idx > aerial_idx then
    LazyVim.warn("The `edgy.nvim` extra must be **imported** before the `aerial.nvim` extra to work properly.", {
      title = "LazyVim",
    })
  end

  opts.right = opts.right or {}
  table.insert(opts.right, {
    title = "Aerial",
    ft = "aerial",
    pinned = true,
    open = "AerialOpen",
  })
end
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "folke/edgy.nvim",
  optional = true,
  opts = function(_, opts)
    local edgy_idx = LazyVim.plugin.extra_idx("ui.edgy")
    local aerial_idx = LazyVim.plugin.extra_idx("editor.aerial")

    if edgy_idx and edgy_idx > aerial_idx then
      LazyVim.warn("The `edgy.nvim` extra must be **imported** before the `aerial.nvim` extra to work properly.", {
        title = "LazyVim",
      })
    end

    opts.right = opts.right or {}
    table.insert(opts.right, {
      title = "Aerial",
      ft = "aerial",
      pinned = true,
      open = "AerialOpen",
    })
  end,
}
```

</TabItem>

</Tabs>

## [lualine.nvim](https://github.com/nvim-lualine/lualine.nvim) _(optional)_

 lualine integration


<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = function(_, opts)
  if not vim.g.trouble_lualine then
    table.insert(opts.sections.lualine_c, {
      "aerial",
      sep = " ", -- separator between symbols
      sep_icon = "", -- separator between icon and symbol

      -- The number of symbols to render top-down. In order to render only 'N' last
      -- symbols, negative numbers may be supplied. For instance, 'depth = -1' can
      -- be used in order to render only current symbol.
      depth = 5,

      -- When 'dense' mode is on, icons are not rendered near their symbols. Only
      -- a single icon that represents the kind of current symbol is rendered at
      -- the beginning of status line.
      dense = false,

      -- The separator to be used to separate symbols in dense mode.
      dense_sep = ".",

      -- Color the symbol icons.
      colored = true,
    })
  end
end
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "nvim-lualine/lualine.nvim",
  optional = true,
  opts = function(_, opts)
    if not vim.g.trouble_lualine then
      table.insert(opts.sections.lualine_c, {
        "aerial",
        sep = " ", -- separator between symbols
        sep_icon = "", -- separator between icon and symbol

        -- The number of symbols to render top-down. In order to render only 'N' last
        -- symbols, negative numbers may be supplied. For instance, 'depth = -1' can
        -- be used in order to render only current symbol.
        depth = 5,

        -- When 'dense' mode is on, icons are not rendered near their symbols. Only
        -- a single icon that represents the kind of current symbol is rendered at
        -- the beginning of status line.
        dense = false,

        -- The separator to be used to separate symbols in dense mode.
        dense_sep = ".",

        -- Color the symbol icons.
        colored = true,
      })
    end
  end,
}
```

</TabItem>

</Tabs>

<!-- plugins:end -->
