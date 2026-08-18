# Usage

Open `src/index.html` in a browser, or use the deployed copy at
[elblogdeismael.github.io/diffchecker](https://elblogdeismael.github.io/diffchecker/).
Nothing is uploaded: the comparison runs in the page.

## The three tabs

| Tab | What it is for |
| --- | --- |
| **Editar** | The two inputs. Paste, drop a file on either panel or pick one |
| **Diferencias** | The comparison, and where blocks are chosen |
| **Resultado** | The merged text, ready to copy or download |

## Comparing

Put a text on each side and open **Diferencias**. Two layouts:

- **Lado a lado** puts the two versions in parallel columns. Best for reading.
- **Unificada** interleaves them in one column, the way `diff -u` prints.

`◀ Todo` and `Todo ▶` take one whole side; `⇄ Intercambiar` swaps left and
right; and the arrows next to the counter jump between changes without
scrolling.

## Options

| Option | Effect |
| --- | --- |
| **Ignorar espacios** | Runs of whitespace inside a line stop counting as differences |
| **Ignorar mayúsculas** | Case is ignored when comparing |
| **Recortar líneas** | Leading and trailing whitespace on each line is dropped before comparing |

The options change what counts as equal, not what is written out: the merged
result always keeps the original text of the block that was chosen.

## Merging

In **Diferencias**, each changed block has a control to keep the left side, the
right side or both. `Limpiar` resets every choice. The **Resultado** tab shows
what those choices produce, and its buttons copy it to the clipboard or download
it as a file.

Two things worth knowing about the file that comes out:

- Line endings are normalised to `LF`.
- What gets written is built from the same rows that are on screen, so what you
  read is what you get. That is the property the test suite exists to protect.

## Files

Drop a file on either panel or use the picker. Everything stays in the page —
there is no server to send it to. The names shown above each panel are used as
labels only.

`Ejemplo` loads a short pair to see the tool working without pasting anything.
