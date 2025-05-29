# Week 7: Versioning & CI/CD with DVC & CML

## Day 1: Introduction to DVC and Project Setup

1. **Install DVC**  
   ```bash
   pip install dvc
   ```
2. **Initialize DVC in Your Repo**  
   ```bash
   git init
   dvc init
   ```  
   This creates `.dvc/` and `.dvcignore` to track data and pipelines.

3. **Configure Remote Storage**  
   ```bash
   dvc remote add -d storage s3://my-bucket/path
   ```  
   Sets up a remote cache to store large datasets and model artifacts.

---

## Day 2: Data and Model Versioning

1. **Track Data Files**  
   ```bash
   dvc add data/raw/data.csv
   git add data/raw/data.csv.dvc .gitignore
   git commit -m "Track raw dataset with DVC"
   ```  
   Generates `.dvc` file describing the data version.

2. **Push Data to Remote**  
   ```bash
   dvc push
   ```  
   Uploads the actual data to the configured remote storage.

3. **Pull Data in Another Environment**  
   ```bash
   git clone <repo_url>
   dvc pull
   ```  
   Retrieves versioned data to reproduce experiments elsewhere.

---

## Day 3: Experiment Tracking and Pipelines

1. **Create a DVC Pipeline**  
   ```bash
   dvc run -n train_model \
       -d src/train.py -d data/processed \
       -o models/model.pkl \
       python src/train.py
   ```  
   Defines stages with dependencies and outputs for reproducibility.

2. **Inspect Pipeline Status**  
   ```bash
   dvc status
   ```  
   Checks which stages are outdated or need re-running.

3. **Reproduce Pipeline**  
   ```bash
   dvc repro
   ```  
   Automatically runs only necessary stages to update outputs.

---

## Day 4: Introduction to CML and GitHub Actions

1. **Install CML**  
   ```bash
   pip install cml
   ```  
   Integrates ML experiments into CI/CD workflows.

2. **Add GitHub Workflow**  
   Create `.github/workflows/cml.yaml`:
   ```yaml
   name: cml
   on: [push]
   jobs:
     build:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v3
         - uses: iterative/setup-cml@v1
           with:
             python-version: '3.9'
         - run: pip install dvc cml
         - run: dvc pull && dvc repro
         - run: pytest --maxfail=1 --disable-warnings -q
         - run: |
             python scripts/generate_metrics.py --output metrics.md
         - run: cml-send-comment metrics.md --md
   ```

---

## Day 5: Automating Metrics and Reports

1. **Generate Metrics**  
   ```python
   import pandas as pd

   metrics = {"accuracy": 0.95, "loss": 0.05}
   pd.DataFrame([metrics]).to_markdown("metrics.md", index=False)
   ```
2. **Publish to PR Comments**  
   ```bash
   cml-send-comment metrics.md --md
   ```

---

## Day 6: Advanced CI/CD for Data Science

1. **Test Data Diffs with CML**  
   ```bash
   dvc diff --targets data/raw > diff.md
   cml-send-comment diff.md --md
   ```
2. **Auto-Deploy Models**  
   Add to workflow:
   ```yaml
   - name: Deploy model
     if: github.ref == 'refs/heads/main'
     run: |
       dvc push
       aws s3 cp models/model.pkl s3://my-bucket/models/
   ```

---

## Day 7: Mini-Project — Reproducible ML Pipeline

**Project Description**  
Set up an ML repo that:
- Versions data with DVC  
- Defines a DVC pipeline for preprocessing, training, evaluation  
- Integrates CML in GitHub Actions to run experiments and report metrics  
- Pushes updated data and models to remote storage  

**Deliverables**  
- `dvc.yaml` and `.dvc` files  
- `.github/workflows/cml.yaml`  
- `metrics.md` generated and posted in CI  
- **README.md** documenting setup, usage, CI/CD process  

```bash
pip install -r requirements.txt
dvc repro
```  
