# Contributing

Thanks for contributing to Tech With Brylie. This project uses [mise](https://mise.jdx.dev/) to provide a consistent Python, uv, and TeX environment, and [uv](https://docs.astral.sh/uv/) to manage the Python project.

## Prerequisites

Install mise and activate it in your shell. See the [mise installation guide](https://mise.jdx.dev/getting-started.html) for your platform.

The commands below assume you are running them from the repository root.

## Get started

1. Clone the repository and enter it.

   ```sh
   git clone <repository-url>
   cd tech-with-brylie
   ```

2. Install the tools declared in [`mise.toml`](mise.toml).

   ```sh
   mise install
   ```

   This installs the pinned Python version, uv, and TinyTeX. Confirm that mise has loaded them:

   ```sh
   mise exec -- python --version
   mise exec -- uv --version
   ```

3. Install `dvisvgm`, which Manim uses to render LaTeX into SVG. It is a TeX Live package rather than a separate mise tool.

   ```sh
   mise exec -- tlmgr install dvisvgm
   mise exec -- dvisvgm --version
   ```

4. Create the project environment and install locked Python dependencies.

   ```sh
   mise exec -- uv sync
   ```

5. Confirm the environment is ready.

   ```sh
   mise exec -- uv run python --version
   mise exec -- dvisvgm --version
   ```

## Recording voiceovers

Recording narration directly during a Manim render uses Manim Voiceover's `recorder` extra. This is optional: it is not needed when using a text-to-speech service or recording narration in another application.

The recorder uses PyAudio, which in turn requires PortAudio. Install the platform dependency before adding the Python extra:

| Platform | Install PortAudio |
| --- | --- |
| macOS | `brew install portaudio` |
| Debian or Ubuntu | `sudo apt install portaudio19-dev` |
| Windows | No separate PortAudio install is normally needed; PyAudio ships with it. |

Then add the recorder support to the project:

```sh
mise exec -- uv add "manim-voiceover[recorder]"
```

If a macOS build still reports that `portaudio.h` cannot be found, retry with Homebrew's explicit include and library paths:

```sh
CFLAGS="-I$(brew --prefix portaudio)/include" \
LDFLAGS="-L$(brew --prefix portaudio)/lib" \
mise exec -- uv add "manim-voiceover[recorder]"
```

Record narration as short `with self.voiceover(...)` segments. Each accepted segment is cached, so correcting a later line does not require re-recording earlier narration.

## Everyday development

Run Python commands through uv so they use the project environment:

```sh
mise exec -- uv run python
```

For example, once a Manim scene is available, render it with:

```sh
mise exec -- uv run manim path/to/scene.py SceneName
```

## Dependency changes

Use uv to add or remove Python dependencies. Commit both `pyproject.toml` and `uv.lock` when dependencies change.

```sh
mise exec -- uv add <package>
mise exec -- uv remove <package>
```

If you change a tool version in `mise.toml`, run `mise install` again and commit the updated configuration (and `mise.lock`, if one is created).

## Before opening a pull request

Make sure the project environment resolves cleanly and that any relevant renders or checks succeed:

```sh
mise exec -- uv sync --locked
```

Keep changes focused, include the source files needed to reproduce any rendered output, and describe the visual result in the pull request.
