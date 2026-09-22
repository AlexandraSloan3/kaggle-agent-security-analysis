# Manual upload checklist

This file is a guide for you. **Do not upload `UPLOAD_CHECKLIST.md` itself** — delete it from the folder before uploading, or just skip it.

## What is in this zip

```text
README.md
requirements.txt
.gitignore
presentation/
  kaggle-agent-security-defense.pptx
  kaggle-agent-security-defense.pdf
notebooks/
  sub.ipynb
  validation.ipynb
  solution_walkthrough.ipynb
src/
  attack.py
  attack_annotated.py
docs/
  solution.md
  reproduction.md
  evidence.md
  provenance.md
```

The certificate image is **not** in this zip. Its links in `README.md` and `docs/evidence.md` point to `assets/kaggle-bronze-certificate.jpg`, so add your own original image at that path (step 3 below).

## Steps (GitHub web interface)

1. **Unzip locally first.** GitHub's "Upload files" does not unpack a `.zip` — it would store the zip as a single file. Extract the folder on your computer, then upload its contents.

2. **Open your repo** at `github.com/AlexandraSloan3/kaggle-ai-agent-security-bronze` → **Add file** → **Upload files**.

3. **Add the certificate.** Create the `assets/` folder by uploading your original `kaggle-bronze-certificate.jpg` into a folder named `assets` (in the upload box, drag the file and rename it to `assets/kaggle-bronze-certificate.jpg`, or upload it after creating the path). Use your own original image, not any copy that passed through a chat upload.

4. **Drag in the rest.** Select every file and folder from the unzipped folder (README.md, requirements.txt, .gitignore, and the `presentation/`, `notebooks/`, `src/`, `docs/` folders) and drop them into the upload box. They will overwrite the same-named files already in the repo.

5. **Commit.** Add a short commit message (e.g. "Add presentation and update docs") and commit to `main`.

6. **Check the links.** Open the repo README on GitHub and click through: the presentation PDF, the certificate image, and the `docs/` links should all resolve.

## Notes

- `.gitignore` is a hidden file. If your unzip tool hides it, enable "show hidden files" so it uploads too.
- Nothing in these files contains a private path, key, or personal document.
- If you would rather keep the certificate out of the repo entirely, remove the certificate links from `README.md` (Award record row) and `docs/evidence.md` before uploading.
