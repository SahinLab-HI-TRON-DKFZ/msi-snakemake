# ExomePipe‑MSI (Snakemake)

Batch MSI calling for tumor/normal exome BAMs using **msisensor‑pro**. The workflow:
1) builds the microsatellite **site** file once from your reference,
2) auto‑pairs tumor/normal BAMs by filename tags (e.g., `FF_TMA` vs `PB_NOR`),
3) runs `msisensor-pro msi` per pair, and
4) writes the four outputs per pair: `<prefix>_dis`, `<prefix>_germline`, `<prefix>_somatic`, `<prefix>`.

## File structure
```
.
├── Snakefile
├── config/
│   └── config.yaml
├── envs/
│   └── msisensor.yaml
└── logs/               # created automatically
```

## Requirements
- Snakemake (≥7.x recommended)
- Conda/Mamba (for `--use-conda`) **or** msisensor‑pro & samtools on PATH
- `msisensor-pro` v1.3.0 (default in the env)

## Quick start
```bash
# (optional) point to your custom msisensor-pro
export MSISENSOR_CMD="/media/NAS/Softwares/msisensor-pro-v1.3.0"  # dir or full binary

# edit config/config.yaml for your paths
# dry-run
snakemake -n -q

# visualize DAG (optional)
snakemake --dag | dot -Tpng > dag.png

# run
snakemake --use-conda --cores 8
```

## Pairing logic
The workflow expects names like:
```
<pre>_FF_TMA_<post>_S<digits>....bam   # tumor
<pre>_PB_NOR_<post>_S<digits>....bam   # normal
```
and produces `results/<pre>_<post>*`. Change tags in `config.yaml` (`tumor_tag`, `normal_tag`). Set `recursive: true` to crawl subfolders.

## Config options (config/config.yaml)
- `reference`: FASTA (e.g., `human_g1k_v37.fasta`)
- `bam_dir`: directory with BAMs
- `outdir`: output root (default `results`)
- `tumor_tag` / `normal_tag`: filename tokens to match pairs
- `recursive`: search subfolders for BAMs
- `msisensor_cmd` / `samtools_cmd`: explicit tool paths (or use env vars `MSISENSOR_CMD` / `SAMTOOLS_CMD`)
- `msi_threads`: threads for the MSI step

## Outputs per pair
For each matched prefix `<pre>_<post>` in `{outdir}`:
```
<prefix>_dis
<prefix>_germline
<prefix>_somatic
<prefix>
```
Logs are in `logs/<prefix>.msi.log`.

---

## Author

**Sakshi Singh**  
Email: [sakshi.singh@dkfz-heidelberg.de](mailto:sakshi.singh@dkfz-heidelberg.de)  
Developer and pipeline designer at SahinLab-HI-TRON-DKFZ  
