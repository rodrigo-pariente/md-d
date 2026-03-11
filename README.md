# Markdown-Database [md-d]

<p align="center">
  <img src="https://github.com/rodrigo-pariente/md-d/blob/main/images/logo.png" alt="md-d logo"  />
</p>
<p align="center"><b>Search and retrieve personal Markdown notes from the command-line!</b></p>


## How to use

Display a note:

`$ md-d my_awesome_note.md`

The `.md` extension is optional — md-d already knows you're searching for a Markdown file.

Print a section of a note:

`$ md-d -c "Stoicism" philosophy.md`

Since the section searching expression relies on grep, you can also use grep-compatible regex like:

`$ md-d -c "[Ss]toi.*" philosophy.md`

To see all notes in your database:

`$ md-d -l -d awesome-notes`

To see a summary of a Markdown:

`$ md-d -s my-big-markdown.md`

And to see all of the definition lists:

`$ md-d -g my-markdown-glossary.md`

Since it also has stdin support you can do things like:

`$ md-d -s <classical-thinkers.md`

But calling the file directory as a database is preferable:

`$ md-d -d . -s classical-thinkers.md`

For more usage information:

`$ md-d -h`

> [!NOTE]
> A "database" is simply a directory containing Markdown files, so you can
> search through non-configured databases just by giving its directory!

## Install

Requirements:

- Bash ≥ 4.2
- Standard Unix utilities:
  - grep
  - wc
  - cat
  - mktemp
  - tail
- Optional renderer:
  - bat / batcat

Steps:

```
git clone 'https://github.com/rodrigo-pariente/md-d'
cd md-d
./install
```

The installer creates two directories: `$HOME/.config/md-d` for storing the configuration file and `$HOME/.local/share/md-d` as the main Markdown database.

That's it, you are ready to easily recall your Markdown annotations.

> [!NOTE]
> Don't be afraid to tweak and hack not only the program configuration, but
> also its algorithm.

## Configuration

The global configuration is sourced as `$HOME/.config/md-d/config`, but each database can have a local configuration file under the path `.../database/.md-d.config`. Local configuration has precedence over the global configuration.

The configuration files are written in bash such as the following:

```bash
# vi: ft=bash # recommended if you use a vi-like editor

# Change the appearence of the summary made of your Markdown
MD_TITLE_COLORS=( "\e[0m" "\e[32m" "\e[35m" "\e[30m" "\e[30m" "\e[30m" )
SUMMARY_SPACING="  "
# SUMMARY_ SPACING="\t" # Indent summary chapters with tabs instead of spaces
SUMMARY_BULLET_POINT="- "

# Function loaded after input is parsed
load_defaults() {
  # Set a default renderer
  # Using batcat as default renderer is highly recommended for pretty printing!
  if [[ -z "$renderer" ]]; then
    renderer="batcat"
  fi

  # Set default options to be chosen with a specific renderer
  if [[ "$renderer" == batcat && -z "$options" ]]; then
    options=("--no-pager")
  fi
}

# Load functions to override default behaviour
load_overrides() {
  . "$HOME/.config/md-d/numbered_summary"
  . "$HOME/.config/md-d/my_custom_get_section"
}

# Configure your Markdowns databases
# Exclusive of the global configuration
declare -A databases=(
  # the `main` database is the default database for when none is specified
  [main]="$HOME/.local/share/md-d/"
  [awesome-notes]="$HOME/.../MyAwesomeMarkdowns/"
  [database-name]="directory/of/markdowns"
)
```

## Limitations

Markdown has a lot of variations and some of the features presented here may
lack in compatibility with **Setext-Style Headers** and **Multiline definitions list**.
