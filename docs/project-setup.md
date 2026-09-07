# Project setup guide<!-- omit in toc -->

Be ready **before lab session 2**: accounts, the course GitHub repo, a team DagsHub project, and a local [cookiecutter data science](https://drivendata.github.io/cookiecutter-data-science/) project managed with [uv](https://docs.astral.sh/uv/).

This guide does **not** replace the DVC or MLflow demos. You install those packages here; you configure remotes and tracking when you follow those walkthroughs.

## Table of contents<!-- omit in toc -->
- [Ready when](#ready-when)
- [Once per team](#once-per-team)
- [Every member](#every-member)
- [Local project (cookiecutter + uv)](#local-project-cookiecutter--uv)
- [Next](#next)

## Ready when

- [ ] Every member has GitHub, DagsHub, and Hugging Face accounts
- [ ] Every member can use Git over SSH with GitHub
- [ ] The team GitHub repo `taed2-<team-name>` exists under [taed2-2627q1-gced-upc](https://github.com/taed2-2627q1-gced-upc) and all members can access it
- [ ] A DagsHub project with the **same name** exists; all members are collaborators; each member has a personal DagsHub token
- [ ] Every member can open Google Colab **or** Kaggle (for the connectivity ping)
- [ ] The team has some shared board + chat (any tools you will actually use)
- [ ] A cookiecutter + uv project is pushed to the course GitHub remote, with `dvc[s3]`, `mlflow`, and the listed dev tools installed locally

> [!IMPORTANT]
> Canonical M1 and M2 use **GitHub** and a **DagsHub project**. You do **not** need a Team VM before lab session 2 (or for canonical M2). FIB VMs are for M4 deploy; self-hosting on the VM is an [extra demo](deployment/00_team_object_store.md).

> [!IMPORTANT]
> On Windows 10/11, use [WSL](https://learn.microsoft.com/en-us/windows/wsl/install) for this course’s local tooling.

## Once per team

### 1. Create the GitHub repo

In the course org [taed2-2627q1-gced-upc](https://github.com/taed2-2627q1-gced-upc), create `taed2-<team-name>` and add all teammates.

Create it **empty**: do **not** add a README, license, or `.gitignore` in the GitHub UI (those fight the first push from cookiecutter).

Git for the student project stays on GitHub. Do not use DagsHub as the Git remote.

### 2. Create the DagsHub project

On [DagsHub](https://dagshub.com/), one member creates a repository named like the GitHub repo (`taed2-<team-name>`) and adds the other members as collaborators. Only collaborators can push DVC data and log MLflow runs. Do not wait for a course-provisioned DagsHub org.

You will copy remote / MLflow URI details from that project’s **Remote** UI later in the [DVC](dvc-demo.md) and [MLflow](mlflow-demo.md) demos. Do not log student runs on the instructor DagsHub project.

### 3. Board and chat

Pick any shared board and chat the team will actually use. Staff look for evidence of coordination, not a specific product.

## Every member

1. Create accounts on [GitHub](https://github.com/), [DagsHub](https://dagshub.com/), and [Hugging Face](https://huggingface.co/) if you do not have them yet.
2. Connect Git to GitHub with [SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh).
3. Create a personal token under [DagsHub user settings](https://dagshub.com/user/settings/tokens). Never commit tokens or share one team-wide token.
4. Confirm you can open **Colab or Kaggle** and use that host’s secrets (not notebook cells) for credentials when you run the ping.
5. Clone the team GitHub repo when it has content (after the local project steps below), or skip straight to cookiecutter if you are the member creating the first tree:

    ```bash
    git clone git@github.com:taed2-2627q1-gced-upc/taed2-<team-name>.git
    ```

Hugging Face is needed for dataset and model cards ([model card template](https://huggingface.co/docs/hub/model-card-annotated), [dataset card guide](https://github.com/huggingface/datasets/blob/main/templates/README_guide.md)). Cards are take-home work reviewed in progress reviews and assessed with the first report — not a Friday week-1 deadline. Add an `HF_TOKEN` to a local `.env` later when your code needs it (see [`.env.template`](../.env.template)); keep `.env` out of Git.

## Local project (cookiecutter + uv)

Do this on the machine where you develop (WSL on Windows). One member usually creates the tree and pushes; everyone else clones and runs `uv sync`.

### Prerequisites

- Python 3.11 or higher (uv can install it)
- Git
- [pipx](https://pipx.pypa.io/)
- [cookiecutter data science](https://drivendata.github.io/cookiecutter-data-science/) (`ccds`)
- [uv](https://docs.astral.sh/uv/)

### Steps

1. Install pipx (if needed), then install `ccds` and uv:

    ```bash
    python3 -m pip install --user pipx
    python3 -m pipx ensurepath
    pipx install cookiecutter-data-science
    pipx install uv
    ```

    Restart the shell if `pipx` or `ccds` is not on your `PATH` yet.

2. From the parent directory where you want the project folder, run:

    ```bash
    ccds
    ```

    Prefer a project directory name that matches `taed2-<team-name>`. Select **`uv`** as the environment manager and **`pyproject.toml`** as the dependency file. Other prompts can stay at defaults unless your team agrees otherwise.

3. Enter the project directory:

    ```bash
    cd taed2-<team-name>
    ```

4. Create the environment and lockfile:

    ```bash
    uv sync
    ```

5. Add packages you will use in M1/M2 demos, plus common dev tools:

    ```bash
    uv add 'dvc[s3]' mlflow
    uv add --group dev ruff pytest
    ```

    Configuring the DagsHub DVC remote and the DagsHub tracking URI is **not** done in this guide — follow [DVC](dvc-demo.md) and [MLflow](mlflow-demo.md).

> [!TIP]
> Skim the [uv documentation](https://docs.astral.sh/uv/) once so `uv add`, `uv sync`, and `uv run` are familiar.

6. Point Git at the course-org remote and push. If `ccds` already ran `git init`, do not init again:

    ```bash
    git remote add origin git@github.com:taed2-2627q1-gced-upc/taed2-<team-name>.git
    git add .
    git commit -m "Initial commit"
    git push -u origin main
    ```

    Use your default branch name if it is not `main` (`git branch` to check).

> [!WARNING]
> Use the **SSH** remote URL (`git@github.com:taed2-2627q1-gced-upc/taed2-<team-name>.git`), not HTTPS.

If the GitHub repo was not empty (for example someone added a README in the UI), pull/rebase or recreate an empty repo before the first push — do not force-push over teammates’ work.

## Next

| When you are ready to… | Go to |
| --- | --- |
| Run the M1 **connectivity ping** (Colab or Kaggle → one dummy metric on the team DagsHub tracking URI → screenshot) | [Connectivity ping](mlflow-demo.md#connectivity-ping) |
| Point DVC at the team DagsHub project | [DVC demo](dvc-demo.md) |
| Log real experiments to the DagsHub tracking URI | [MLflow demo](mlflow-demo.md) |
| Use branches and pull requests | [Git demo](git-demo.md) |
