# Unicode byte order marks (BOM)

For general information about BOM's, refer to [this link][1].

## Remove the BOM from Git with a filter

The filters in this section remove the BOM on commit, and add the BOM on
checkout.

### How it works

All of the filter definitions use variations of the following commands, where
'abc' is the appropriate BOM byte sequence:

```bash
# Remove 'abc' from beginning of file
sed -b -e '1!b' -e 's/^abc//'

# Add 'abc' to beginning of file (if not present)
sed -b -e '1!b' -e 's/\(^\|^abc\)/abc/'
```

For a list of BOM byte sequences, refer to [this link][3].

### How to use it

To define a filter, copy and paste the desired filter definitions to a Git
configuration file (**~/.gitconfig** or **.git/config**).

To use a filter, apply the filter in a **.gitattributes** file. For example:

```gitignore
# .gitattributes filter usage example
*.csproj filter=utf8-autobom
```

After defining and applying a filter, run these commands to update the files in
Git:

```bash
rm .git/index
git reset
```

Now `git status` shows modified files that are not staged for commit. `git
checkout <file>` does not undo the modifications because the checkout itself is
subject to our filter (unless the checkout also reverts our .gitattributes
changes). Review the changes using `git diff`, then commit the changes as usual
with:

```bash
git add .
git commit
```

### Filter definitions

Copy and paste these into a Git configuration file (**~/.gitconfig** or
**.git/config**).

#### UTF-8

```ini
[filter "utf8-autobom"]
        clean = sed -b -e '1!b' -e 's/^\\xEF\\xBB\\xBF//'
        smudge = sed -b -e '1!b' -e 's/\\(^\\|^\\xEF\\xBB\\xBF\\)/\\xEF\\xBB\\xBF/'
```

#### UTF-16 big endian

```ini
[filter "utf16be-autobom"]
        clean = sed -b -e '1!b' -e 's/^\\xFE\\xFF//'
        smudge = sed -b -e '1!b' -e 's/\\(^\\|^\\xFE\\xFF\\)/\\xFE\\xFF/'
```

#### UTF-16 little endian

```ini
[filter "utf16le-autobom"]
        clean = sed -b -e '1!b' -e 's/^\\xFF\\xFE//'
        smudge = sed -b -e '1!b' -e 's/\\(^\\|^\\xFF\\xFE\\)/\\xFF\\xFE/'
```

#### UTF-32 big endian

```ini
[filter "utf32be-autobom"]
        clean = sed -b -e '1!b' -e 's/^\\x00\\x00\\xFE\\xFF//'
        smudge = sed -b -e '1!b' -e 's/\\(^\\|^\\x00\\x00\\xFE\\xFF\\)/\\x00\\x00\\xFE\\xFF/'
```

#### UTF-32 little endian

```ini
[filter "utf32le-autobom"]
        clean = sed -b -e '1!b' -e 's/^\\xFF\\xFE\\x00\\x00//'
        smudge = sed -b -e '1!b' -e 's/\\(^\\|^\\xFF\\xFE\\x00\\x00\\)/\\xFF\\xFE\\x00\\x00/'
```

## Tips for Vim

Refer to [this link][2].

### To add a BOM to an open file

```vim
:set bomb
:w
```

### To remove the BOM from an open file

```vim
:set nobomb
:w
```

### To ask Vim if the open file has a BOM

```vim
:set bomb?
```

[1]: http://unicode.org/faq/utf_bom.html#BOM
[2]: http://vim.1045645.n5.nabble.com/How-to-display-and-remove-BOM-in-utf-8-encoded-file-td4681708.html
[3]: http://unicode.org/faq/utf_bom.html#bom4
