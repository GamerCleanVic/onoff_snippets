# onoff_snippets

⚠️ HÁ A NECESSIDADE DE TESTAR EM VM E CONSERTAR OS ERROS, POIS ESSE CÓDIGO CAUSA ERROS DE ENDEREÇOS DE ARQUIVOS E MOSTRA ALERTAS DESSES ERROS NO NEOVIM (AINDA ESTOU APRENDENDO PARA CONSERTAR)

> ## COLAR O CÓDIGO NO INIT
> ```bash
> ~/.config/nvim//init.lua
> ```
>
> CRÓDIGO
> ```lua
> return {
>   -- Outros plugins...
>   { import = "plugins.snippet_toggle" },
> }
> ```

> ## CRIAR UM ARQUIVO LUA NA PASTA ABAIXO
> ```bash
> ~/.config/nvim/lua/plugins/snippet_toggle.lua
> ```
>
> ## COLAR O CÓDIGO NO ARQUIVO ABAIXO
> ```bash
> ~/.config/nvim/lua/plugins/snippet_toggle.lua
> ```
>
> CRÓDIGO
> ```lua
>    -- ~/.config/nvim/lua/plugins/snippet_toggle.lua
>
>    local M = {}
>
>   -- Estado para rastrear se os snippets estão habilitados
>   local snippets_enabled = true
>
>   -- Função para desativar todos os snippets
>   local function disable_snippets()
>       -- Desativa snippets do luasnip (usado por LazyVim)
>       if package.loaded['luasnip'] then
>           require('luasnip').config.set_config({ enable_autosnippets = false })
>           vim.g.snippets = "none"
>       end
>
>       -- Limpa quaisquer snippets ativos
>       if package.loaded['cmp'] then
>           local cmp = require('cmp')
>           cmp.setup({
>               snippet = {
>                   expand = function() end, -- Desativa expansão de snippets
>               },
>               sources = vim.tbl_filter(function(source)
>                   return source.name ~= 'luasnip'
>               end, cmp.get_config().sources)
>           })
>       end
>
>       print("Snippets desativados")
>   end
>
>   -- Função para ativar snippets
>   local function enable_snippets()
>       -- Reativa snippets do luasnip
>       if package.loaded['luasnip'] then
>           require('luasnip').config.set_config({ enable_autosnippets = true })
>           vim.g.snippets = "luasnip"
>       end
>
>       -- Restaura configuração do nvim-cmp com snippets
>       if package.loaded['cmp'] then
>           local cmp = require('cmp')
>           cmp.setup({
>               snippet = {
>                   expand = function(args)
>                       require('luasnip').lsp_expand(args.body)
>                   end,
>               },
>               sources = cmp.get_config().sources
>           })
>           -- Re-adiciona luasnip como fonte se não estiver presente
>           local has_luasnip = false
>           for _, source in ipairs(cmp.get_config().sources) do
>               if source.name == 'luasnip' then
>                   has_luasnip = true
>                   break
>               end
>           end
>           if not has_luasnip then
>               table.insert(cmp.get_config().sources, { name = 'luasnip' })
>               cmp.setup({ sources = cmp.get_config().sources })
>           end
>       end
>
>       print("Snippets ativados")
>   end
>
>  -- Função de toggle
>   function M.toggle_snippets()
>       if snippets_enabled then
>           disable_snippets()
>           snippets_enabled = false
>       else
>           enable_snippets()
>           snippets_enabled = true
>       end
>   end
>
>   -- Configuração do mapeamento de tecla
>   vim.keymap.set('n', '\\', M.toggle_snippets, { noremap = true, silent = true, desc = "Toggle snippets" })
>
>   return M
> ```

Fonte [x.com/i/grok](https://x.com/i/grok 'Grok')
