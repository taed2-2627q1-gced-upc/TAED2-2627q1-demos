# TAED-2 2026-27 — lab demos

Reference project and walkthroughs for **Advanced Topics in Data Engineering II** (UPC). The running example is IMDB sentiment with DistilBERT. Milestones follow Lanubile et al. (see [references](#references)).

## Your team repo

Work in your own repository under [taed2-2627q1-gced-upc](https://github.com/taed2-2627q1-gced-upc). Create `taed2-<team-name>`, add your teammates, and clone that repo for all coursework. **This demo repo is a read-only reference** — do not submit work here.

You can choose a different ML problem. The milestones stay the same: versioned data, tracked experiments, tests, and an API we can run.

## Milestones

| | Practice | Demo |
|---|----------|------|
| **M1** | Hugging Face dataset and model cards; GitHub + board/chat; DagsHub connectivity ping | [Card templates](https://huggingface.co/docs/hub/model-card-annotated), [dataset card guide](https://github.com/huggingface/datasets/blob/main/templates/README_guide.md), [Git](docs/git-demo.md), [connectivity ping](docs/mlflow-demo.md#connectivity-ping) |
| **M2** | Cookiecutter + uv; GitHub Flow + DVC; MLflow on the DagsHub project | [Project setup](docs/project-setup.md), [Git](docs/git-demo.md), [DVC](docs/dvc-demo.md), [MLflow](docs/mlflow-demo.md) |
| **M3** | CodeCarbon; Ruff; Pytest + Great Expectations | [CodeCarbon](docs/codecarbon-demo.md), Ruff (pre-commit in this repo), [Pytest](docs/pytest-demo.md), [GX](docs/great-expectations-demo.md) |
| **M4** | Deploy on the Team VM (or cloud); FastAPI + tests | [Deployment](docs/deployment/), [FastAPI](docs/fastapi-demo.md) |

Default tools are listed in each demo. If you swap a tool, keep the same deliverable (e.g., a remote for DVC, a shared tracking server, an HTTP API with tests).

**Team VM:** FIB allocates one VM per team for **M4** deploy. Default M1/M2 use GitHub and a **DagsHub project**; they do not wait on the VM. Self-hosting DVC and MLflow on the VM can be done following the [Team object store guide](docs/deployment/00_team_object_store.md).

## Optional extras

Not required. Useful if you want to push beyond the default stack:

- Self-hosted DVC/MLflow: [Team object store (AIStor on the Team VM)](docs/deployment/00_team_object_store.md)
- [SHAP](https://shap.readthedocs.io/en/latest/text_examples.html)
- Fairness: [AIF360](https://github.com/Trusted-AI/AIF360), [Fairlearn](https://fairlearn.org/)
- [MLOps tools list](https://agate-tangerine-725.notion.site/MLOps-tools-255624cb2156801e9a98db82fd911da2?pvs=74)

## References

1. F. Lanubile, S. Martínez-Fernández, and L. Quaranta, "Teaching MLOps in Higher Education through Project-Based Learning." SEET@ICSE 2023. [doi:10.1109/ICSE-SEET58685.2023.00015](https://doi.org/10.1109/ICSE-SEET58685.2023.00015)
2. F. Lanubile, S. Martínez-Fernández, and L. Quaranta, "Training future ML engineers: a project-based course on MLOps." IEEE Software 2024. [doi:10.1109/MS.2023.3310768](https://doi.org/10.1109/MS.2023.3310768)
