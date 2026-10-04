# Contributing

Eleven skills and six standard-library Python tools. The rules below are short,
and every one of them is enforced by the test suite, so run it before you open
the pull request.

## Run the tests

```bash
python3 -m unittest discover -s tests -v
```

Python 3.8 or newer. Nothing to install. CI runs the same command on 3.8
through 3.14.

## The rules

**Standard library only.** No `pip install`, no `requirements.txt`, no
third-party imports, in a tool or in a test. Between them the six tools use
`argparse`, `csv`, `datetime`, `json`, `math`, `os`, `re`, `struct`, `sys`,
`unicodedata` and `zlib`, so anything you need is almost certainly in there.

**`%`-formatting, not f-strings.**

```python
out.append("- %-40s %5.1f" % (v["file"], v["score"]))
```

**No em dashes.** Not one U+2014 in any `.md`, `.py`, `.json`, `.ts` or `.csv`
file in this repo. `tests/test_repo.py::test_no_em_dashes_anywhere` walks every
file and fails on the first one it finds. Use a comma, a colon, a full stop, or
a spaced hyphen pair instead.

**Tests live in `tests/`.** One `test_<tool>.py` per tool, named after the skill
folder it covers. Load the script through `tests/_load.py`, because the folder
names have hyphens and cannot be imported normally:

```python
from _load import load

parity = load("replica-diff", "parity")
```

Cover the malformed input, not just the happy path. Every tool has to exit 2 on
input it cannot read, and that is worth a test.

**Match the shape of the existing tools.** A module docstring that opens with
what the tool is and the words `Standard library only.`, then indented usage
lines, then:

```python
ap = argparse.ArgumentParser(description=__doc__.split("\n\n")[0])
```

A `def main(argv=None)`, and `sys.exit(main())` at the bottom. `main` returns 2
for input it cannot read, 0 for clean, and 1 when a gate finds something, which
is what `sweep.py` and `parity.py --fail-under` do so they can block a deploy.

## Adding a skill

The skill count is hard-coded in more than one place, and the tests will tell
you which ones you missed. All of these have to agree:

- the `SKILLS` list in `tests/test_repo.py` (enforced)
- the `ABOUT` string beside it, which must match `description` in
  `.claude-plugin/plugin.json` character for character (enforced)
- the plugin `description` in `.claude-plugin/marketplace.json`, which is a
  third wording of the same sentence
- the tagline and the `## The eleven` heading in `README.md` (the tagline is
  enforced, the heading is not, but update it anyway)
- a row in the README table, and `` `/replica-yourskill` `` somewhere in the
  README body (enforced for every skill)

Run the tests while you edit rather than at the end. They name the exact file
and the exact string.