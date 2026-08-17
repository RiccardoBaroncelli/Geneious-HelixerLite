# Geneious-HelixerLite
HelixerLite Geneious Plugin 1.0.0-beta

Developed by Riccardo Baroncelli.

This plugin predicts eukaryotic genes from the selected Geneious nucleotide sequence or
sequence-list/multifasta document using HelixerLite (https://github.com/nextgenusfs/helixerlite).

CITATION
If you use Helixer, please cite: Holst F, Bolger AM, Kindel F, Günther C, Maß J, Triesch S, Kiel N, Saadat N, Ebenhöh O, Usadel B, Schwacke R. Helixer: ab initio prediction of primary eukaryotic gene models combining deep learning and a hidden Markov model. Nature Methods. 2025 Nov 24:1-8.

LINEAGE MODEL SELECTION
Before each run, an options panel lets you choose which pre-trained Helixer model (lineage) to use:
  - fungi        (Fungi / Ascomycete genomes)
  - land_plant   (Land plants / embryophytes)
  - vertebrate   (Vertebrate animals)
  - invertebrate (Invertebrate animals)
The default is fungi. The chosen value is passed to helixerlite as --lineage.

REQUIREMENTS
  - Geneious Prime 2026 or later.
  - Internet access on first run (to download HelixerLite and its dependencies).
  - On Windows: the Windows Subsystem for Linux (WSL) feature enabled once by an administrator
    (see WINDOWS SUPPORT below). No manual Python or package setup is required.

FIRST-RUN BEHAVIOUR (macOS / Linux)
The first prediction sets HelixerLite up automatically; later runs reuse it.
  1. Look for a plugin-owned HelixerLite installation, then for an existing helixerlite
     executable on PATH.
  2. If missing, create a Python virtual environment under:
       macOS: ~/Library/Application Support/Geneious/HelixerLite/venv
       Linux: ~/.geneious/HelixerLite/venv
  3. Install helixerlite into it (pip install helixerlite).

WINDOWS SUPPORT (via WSL)
HelixerLite is not distributed as a native Windows package: its HelixerPost component is
compiled Rust and is published only as Linux (manylinux) and macOS wheels, so
"pip install helixerlite" cannot succeed on native Windows Python. On Windows this plugin
therefore runs HelixerLite inside the Windows Subsystem for Linux (WSL) and provisions
everything it needs automatically, without touching your system Python or requiring apt/sudo:
  1. If a WSL Linux distribution is already registered, the plugin uses it (your default
     distribution and user are respected). If the WSL feature is enabled but no distribution
     exists, the plugin installs Ubuntu into WSL for you.
  2. Inside WSL, it downloads the standalone "uv" tool and uses it to install a private,
     self-contained CPython 3.11 under ~/.geneious/HelixerLite. HelixerLite publishes wheels
     for CPython 3.9-3.11 only, so a dedicated 3.11 is used to guarantee prebuilt wheels are
     installed rather than compiled from source. This does not depend on the distribution's own
     python3 or on the python3-venv package.
  3. It creates the virtual environment at ~/.geneious/HelixerLite/venv (on the Linux
     filesystem) and installs helixerlite there.
  4. Windows input/output files are translated to WSL paths automatically and helixerlite is
     executed through wsl.exe.
The only step that cannot be automated is enabling the WSL feature itself, which Windows
requires an administrator to do once. If it is not enabled, the plugin shows a message asking
you to open PowerShell as Administrator, run "wsl --install", and reboot; subsequent runs then
proceed automatically.

Note: the very first Windows run downloads Python, HelixerLite and its machine-learning
dependencies (TensorFlow etc.) inside WSL and can take several minutes. Later runs are fast.

RUNTIME COMMAND
helixerlite --fasta selected_geneious_input.fasta --lineage <selected> --out helixerlite_predictions.gff3

NOTES
- Multifasta / sequence-list documents are supported: HelixerLite runs once over the combined
  FASTA and features are routed back to each source sequence by GFF3 seqid.
- GFF3 gene, mRNA/transcript, exon, CDS and UTR features are imported as Geneious annotations.
  Coordinates are imported verbatim (GFF3 and Geneious intervals are both 1-based inclusive).
  Multi-segment CDS/exon features are combined into a single multi-interval annotation ordered
  in translation direction.
- If the plugin fails, it adds a short HelixerLite status annotation containing the error message.

CHANGELOG
1.0.0-beta
  - First public beta. Predicts eukaryotic genes with HelixerLite from single sequences and from
    multifasta / sequence-list documents (one result per sequence), imports gene/mRNA/exon/CDS/UTR
    features with verbatim 1-based coordinates and CDS/exon segments ordered by transcription
    direction, and offers a lineage-model selector (fungi / land_plant / vertebrate / invertebrate)
    with attribution and the Helixer citation shown in the run options window.
  - Cross-platform execution: native on macOS and Linux; on Windows, automatic setup and execution
    inside WSL using a self-contained CPython 3.11 provisioned with uv (no system Python, no apt,
    no sudo), with automatic Windows-to-WSL path translation.
