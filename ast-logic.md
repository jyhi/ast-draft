# AST's Startup Toolkit 0.3 Operating Logic

## All Function List (`/src/`)

### `interface/`

#### `<cli.h>`

- `ast_cli_show ()` (**TBD**)
- `ast_cli_parse_arguments ()`

#### `<gui.h>`

- `ast_gui_show ()`

**NOTE:** GUI will invoke verification related functions from time to time. See `<verify.h>`.

### `logic/`

#### `<run.h>`

AST has 2 modes: **Deploy** and **Startup**. **Deployment** is the procedure to install boot loader, and **Start-up** is the procedure to clean up the system.

- `ast_run_deployment ()`
- `ast_run_startup ()`

### `verify/`

#### `<verify.h>`

- `ast_verify_image ()`
  - `ast_verify_image_header ()`
  - `ast_verify_md5sum ()`
  - `ast_verify_sha1sum ()`
  - `ast_verify_sha256sum ()`
  - `ast_verify_sha512sum ()`

### `sysinfo/`

#### `<sysinfo.h>`

Do we need this?

- ~~`ast_check_system_requirements ()` (**TBD**)~~
- `ast_check_secure_boot ()`

### `backup/`

#### `<backup.h>`

- `ast_backup ()`
  - `ast_backup_partition_table ()`
    - `ast_backup_mbr ()`
    - ~~`ast_backup_gpt ()`~~
  - `ast_backup_windows_loader ()`
    - `ast_backup_ntldr ()`
    - `ast_backup_bootmgr ()`
  - `ast_backup_firmware ()`
    - `ast_backup_uefi ()`

### `install/`

#### `<install.h>`

- `ast_install_windows_loader ()`
- `ast_install_mbr ()`
- `ast_install_uefi ()`
- `ast_install_files_system_drive ()`
- `ast_install_files_esp ()`
- `ast_install_generate_ast_info ()`

### `startup/`

#### `<startup.h>`

- `ast_startup_read_info ()`
- `ast_restore_system ()`
- `ast_restore_mbr ()`
- `ast_restore_uefi ()`

### `utils/`

#### `<output.h>`

All functions are wrappers of `_ast_log ()` except itself.

- `_ast_log ()`
  - `_ast_log_to_console ()` / `_ast_log_to_gui_memo ()`
  - `_ast_log_to_file`
- `ast_output ()`
- `ast_info ()`
- `ast_warn_ask ()`
- `ast_warn_noask ()`
- `ast_error ()`
- `ast_success ()`

#### `<guid.h>`

```c
// A GUID should be defined as:
#define EFI_GLOBAL_GUID {0x8be4df64, 0x93ca, 0x11d2, \
    {0xaa, 0x0d, 0x00, 0xe0, 0x98, 0x03, 0x2b, 0x82}}

typedef struct _GUID {
    uint32_t f1;
    uint16_t f2;
    uint16_t f3;
    char     f4[8];
} GUID;
```

- `ast_guid_tostring ()`

## Calling Tree

- `main`
  - `ast_cli_show` or `ast_gui_show`
    - **(Multiple times)** `ast_verify_image`
    - `ast_run_deployment`
      - `ast_check_system_requirements`
      - `ast_backup`
        - `ast_backup_firmware`
        - `ast_backup_windows_loader`
        - `ast_backup_partition_table`
      - `ast_install_windows_loader` or `ast_install_mbr` or `ast_install_uefi`
      - `ast_install_files_system_drive` (and `ast_install_files_esp` if on UEFI platform)
      - `ast_install_generate_ast_info`
    - `ast_run_startup`
      - `ast_startup_read_info`
      - `ast_restore_system`
      - **(If needed)** `ast_restore_mbr` or/and `ast_restore_uefi`
