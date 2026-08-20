# MLX troubleshooting

- **The helper says MLX is not installed:** run `./setup-mlx.sh` from the
  `mlx/` directory.
- **The alias is missing:** run `source "$HOME/.zshrc"` after adding the alias,
  or use the script by its path.
- **Port `8080` is busy:** inspect it with
  `lsof -nP -iTCP:8080 -sTCP:LISTEN` before stopping anything.
- **The model does not load:** confirm that the artifact is MLX-compatible with
  the installed `mlx-lm` and that enough unified memory is available.
- **Memory pressure grows:** stop the server, choose a smaller artifact or
  shorter context, and close memory-heavy applications.
- **A model is missing from the menu:** pass its repository or local directory
  explicitly with `--model`. The menu only lists cached `mlx-community` models.

Use the installed command's `--help` output and the [official server
documentation](https://github.com/ml-explore/mlx-lm/blob/main/mlx_lm/SERVER.md)
when a flag or behavior has changed.
