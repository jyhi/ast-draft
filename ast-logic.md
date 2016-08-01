# AST's Startup Toolkit 0.3 Operating Logic

## All Function List

### `<cli.h>`

- `ast_cli_show ()` (**TBD**)
- `ast_cli_parse_arguments ()`

### `<gui.h>`

- `ast_gui_show ()`

### `<run.h>`

- `ast_run_deployment ()`
- `ast_run_startup ()`

### `<verify.h>`

- `ast_verify_image ()`
  - `_ast_verify_image_header ()`
  - `ast_verify_md5sum ()`
  - `ast_verify_sha1sum ()`
  - `ast_verify_sha256sum ()`
  - `ast_verify_sha512sum ()`

### `<sysinfo.h>`

- `ast_check_system_requirements ()` (**TBD**)

### `<backup.h>`

- `ast_backup ()`
  - `ast_backup_partition_table ()`
    - `ast_backup_mbr ()`
    - ~~`ast_backup_gpt ()`~~
  - `ast_backup_windows_loader ()`
    - `ast_backup_ntldr ()`
    - `ast_backup_bootmgr ()`
  - `ast_backup_firmware ()`
    - `ast_backup_uefi ()`

### `<install.h>`

- `ast_install_windows_loader ()`
- `ast_install_mbr ()`
- `ast_install_uefi ()`
- `ast_install_files_system_drive ()`
- `ast_install_files_esp ()`
- `ast_install_generate_ast_info ()`

### `<startup.h>`

- `ast_startup_read_info ()`
- `ast_restore_system ()`
- `ast_restore_mbr ()`
- `ast_restore_uefi ()`

### `<utils.h>`

- `_ast_log ()`
  - `_ast_log_to_console ()` / `_ast_log_to_gui_memo ()`
  - `_ast_log_to_file`
- `ast_output ()`
- `ast_info ()`
- `ast_warn_ask ()`
- `ast_warn_noask ()`
- `ast_error ()`
- `ast_success ()`
