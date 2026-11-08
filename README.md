# Netlist Schematic Maker

Turn a SPICE netlist into a clean circuit schematic that is already the right size for a journal page.

**Live site:** https://mufasir-fazili.github.io/

Drawing circuits for a paper usually means fighting with sizes: a figure made in PowerPoint or a schematic editor gets shrunk to a column, and the text and lines end up too small or blurry. This tool works the other way round. You choose the page width first, and it draws the schematic so that the line weights and text are the real sizes you asked for (for example 0.75 pt lines and 8 pt text) at the printed size.

Everything runs in your browser. Your netlist is never uploaded anywhere.

## What it does

- Reads a SPICE netlist and draws the schematic automatically.
- Sets the drawing to an exact width in millimetres, using presets for common journals.
- Keeps lines and text at true point sizes, so nothing needs rescaling in your paper.
- Exports **SVG**, **PDF** (vector) and **PNG** (600 dpi).
- Converts text to outlines by default, so Ω, µ and the font look the same on every computer.
- Lets you fix the layout by hand: drag a part to move it, or double-click it to change its name or value.
- Lets you build a circuit without a netlist in **Draw** mode: drag parts from a palette onto a grid, click pins to wire them, and add lines, circles, triangles, rectangles and text. Undo and redo are included.
- Explains problems in plain words, for example "Line 4: R2 has no value", and clicking a message highlights that line.

## How to use it

1. Open the site and pick an example, or paste your own netlist.
2. Choose a **Page width**. The presets are:

   | Preset | Width |
   |---|---|
   | IEEE single column | 88.9 mm |
   | IEEE double column | 181.6 mm |
   | Elsevier single column | 90 mm |
   | Elsevier 1.5 column | 140 mm |
   | Elsevier double column | 190 mm |
   | Springer single column | 84 mm |
   | Springer double column | 174 mm |
   | Thesis / A4 text width | 150 mm |
   | Custom | any width you enter |

3. Set the **Line** and **Text** sizes in points. The defaults are 0.75 pt and 8 pt. Check your journal's author guidelines for its own minimums.
4. Choose a **Drawing size**:
   - *Fill the page width exactly* makes the figure exactly as wide as the preset. Small circuits get large symbols.
   - *Natural size* stops the symbols from growing past a normal size, so a small circuit gives a narrower figure.
5. Drag parts to tidy the layout. Tick **Show at print size** to preview the figure at its real size.
6. Download the SVG, PDF or PNG.

### Where do I get a netlist?

- **LTspice:** open your schematic, then choose View, then SPICE Netlist. Copy the text and paste it into the tool.
- **KiCad:** in the schematic editor choose File, then Export, then Netlist, pick the SPICE format, and open the saved file in a text editor.
- **Other programs:** look for an export or "netlist" command and choose SPICE. Menu names change between versions.
- **No simulator:** use **Add a part** and fill in the form.

## Supported parts

| Letter | Part | Netlist line |
|---|---|---|
| R | Resistor | `R1 in out 10k` |
| C | Capacitor | `C1 out 0 100n` |
| L | Inductor | `L1 a b 10u` |
| D | Diode (LED and zener if the model name says so) | `D1 a k 1N4148` |
| V | Voltage source | `V1 in 0 5` or `Vin in 0 SIN(0 1 1k)` |
| I | Current source | `I1 a 0 1m` |
| Q | NPN or PNP transistor | `Q1 c b e 2N3904` (collector, base, emitter) |
| M | NMOS or PMOS transistor | `M1 d g s b NMOS` (drain, gate, source, body) |
| X | Op-amp | `X1 in+ in- out model` or `X1 in+ in- v+ v- out model` |

Other element types (dependent sources E, F, G, H, and other subcircuits) are skipped, and the tool tells you which lines it left out.

### Conventions the tool follows

- Node `0` and names starting with `gnd` are ground. Ground is drawn as one ground bus with a single symbol.
- A DC voltage source from a supply-style net to ground, such as `VCC vcc 0 12`, is drawn as a supply rail labelled with its name and value instead of a battery. Untick **Supplies as rails** to draw it as a source.
- Nets named like `in`, `out`, `vin` or `vout` get an open terminal and a label, unless a source is already attached.
- A transistor is a PNP or PMOS if its `.model` line says so, or if its name is a well-known one such as `2N3906`.
- Names like `R1`, `Cin` and `VCC` are shown with subscripts, as R<sub>1</sub>, C<sub>in</sub> and V<sub>CC</sub>.

### Saved layout

When you move or rotate a part, the tool writes a comment line at the end of your netlist:

```
*@ R1 x=9 y=-12 rot=90 flip=0 side=1
```

These lines are ignored by SPICE programs. Paste the netlist back later and your layout comes back with it.

## Known limits

The layout is placed automatically with a set of rules, and it works best on amplifiers, filters, and similar circuits that flow from left to right. It has been tested on about 14 circuits:

- It draws well: RC and LC filters, single and two-stage transistor amplifiers, op-amp amplifiers (inverting, summing), a MOSFET stage, a current mirror, and a rectifier.
- It is weaker on a differential pair, a Sallen-Key filter, a non-inverting amplifier and a buck converter. The connections are correct, but the placement is untidy and may need some dragging.
- Negative supply rails are drawn at the top, where the usual convention is the bottom.
- There are no symbols yet for transformers, switches, potentiometers, polarized capacitors, JFETs, or generic ICs.
- The tool does not simulate anything. It only draws.

## Running it yourself

There is nothing to build or install. The whole tool is the single file `index.html`.

- **Locally:** open `index.html` in a browser.
- **On GitHub Pages:** keep the file named `index.html` in the root of your `username.github.io` repository.

An internet connection is needed the first time for these items, which are loaded from public CDNs:

- [jsPDF](https://github.com/parallax/jsPDF) and [svg2pdf.js](https://github.com/yWorks/svg2pdf.js), for PDF export.
- [opentype.js](https://github.com/opentypejs/opentype.js), for turning text into outlines.
- IBM Plex fonts from Google Fonts, for the page itself.

If these cannot load, for example because of an ad blocker, SVG and PNG export still work, but PDF export and text outlines are unavailable.

## Credits and licence

The code is released under the [MIT License](LICENSE).

The text in exported drawings uses subsets of Liberation Serif and Liberation Sans, which are metric-compatible with Times New Roman and Arial. They are embedded in the page and are licensed under the SIL Open Font License 1.1. The libraries listed above are under their own open-source licences.
