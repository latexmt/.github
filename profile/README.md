# LaTeXMT

LaTeXMT is a system for reliable machine translation of LaTeX documents using
on-device machine translation models or public services.

The application was developed as part of a bachelor's thesis at the University
of Innsbruck between September 2024 and March 2025.

A live demo is available (as of March 2025) at
<https://latexmt-informatik.uibk.ac.at/>

## Installation

LaTeXMT is written entirely in Python 3 (>= 3.10). Each of the repositories
[core](https://github.com/latexmt/core),
[cli](https://github.com/latexmt/cli) and
[web](https://github.com/latexmt/web) contains a Python package, with
the latter two depending on `core/`. These modules can be installed using
`pip`:

```sh
pip install \
    'git+https://github.com/latexmt/core.git#egg=latexmt-core[deepl,openai]' \
    'git+https://github.com/latexmt/cli.git' \
    'git+https://github.com/latexmt/web.git'
```

### Hardware Acceleration

LaTeXMT uses PyTorch to enable local machine translation, and can thus utilise
attached GPU for hardware acceleration. As PyTorch ships with CUDA by default,
**no further configuration** is required for **Nvidia** GPUs.

For **AMD** systems, append the following to the command line above:

```sh
--extra-index-url https://download.pytorch.org/whl/rocm6.2.4
```

An experimental version of PyTorch is available for **Intel** GPUs:

```sh
--extra-index-url https://download.pytorch.org/whl/test/xpu
```

To install a minimal version of PyTorch for systems with **no GPU**, use the
following:

```sh
--extra-index-url https://download.pytorch.org/whl/cpu
```

See also: <https://pytorch.org/get-started/locally/>,
<https://pytorch.org/docs/stable/notes/get_start_xpu.html>

## Usage

LaTeXMT can be used either via a command-line interface (CLI) (provided by
`latexmt-cli`), or a Flask web interface (provided by `latexmt-web`).

### CLI

**Example usage**:

```sh
latexmt \
    --src-lang de \
    --tgt-lang en \
    --translator opus \
    --aligner opus \
    --opus-model-base /tmp/latexmt/model/de-en \
    --glossary-file ./glossary.csv \
    --output-root /tmp/latexmt/out \
    /tmp/latexmt/in/document.tex
```

Run `latexmt --help` for additional information.

## Web

The web interface can be started with the following command:

```sh
python -m flask -A latexmt_web.server run
```

A config file is required — refer to
[web/config.example.json](https://github.com/latexmt/web/blob/main/config.example.json).

By default, `config.json` in the current working directory is used. It is
possible to specify a different config file via the environment variable
`LATEXMT_CONFIG_PATH`:

```sh
LATEXMT_CONFIG_PATH=/tmp/latexmt/web/config.json python -m flask -A latexmt_web.server run
```

## Translation Backend Notes

### DeepL/OpenAI/HuggingFace APIs

No API key is provided for DeepL, OpenAI nor HuggingFace API integration, these
must be manually supplied to `latexmt` via the environment variables
`DEEPL_API_TOKEN`, `OPENAI_API_TOKEN` or `HF_API_TOKEN`. For example:

```sh
DEEPL_API_KEY="abcd1234!" latexmt -T deepl [...]
```

## Development

As LaTeXMT is split into packages, a development environment must be set up
where LaTeXMT is installed in _editable mode_.

1. Create a Python virtual environment:

```sh
mkdir -p ~/.local/share/virtualenvs &&
python -m venv ~/.local/share/virtualenvs/latexmt-dev &&
source ~/.local/share/virtualenvs/latexmt-dev/bin/activate
```

2. Clone this repository including all its submodules:

```sh
mkdir latexmt &&
cd latexmt &&
git clone https://github.com/latexmt/core.git &&
git clone https://github.com/latexmt/cli.git &&
git clone https://github.com/latexmt/web.git
```

3. Install LaTeXMT (in editable mode):

```sh
pip install \
    -e ./core[deepl,openai] \
    -e ./cli \
    -e ./web
```

See also: <https://setuptools.pypa.io/en/latest/userguide/development_mode.html>
