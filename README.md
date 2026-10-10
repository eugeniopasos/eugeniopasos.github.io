# Eugenio Pasos — Engineering Portfolio 🚀

Personal engineering portfolio website built for **GitHub Pages** using **Jekyll** and the **[Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/)** theme (`dark` skin).

**Live Site:** [https://eugeniopasos.github.io](https://eugeniopasos.github.io)

---

## 📂 Site Structure

* **`index.md`** — Landing page with hero banner, key statistics, featured project teasers, and categorized technical competencies.
* **`_config.yml`** — Site configuration, metadata, social profiles, and author sidebar settings.
* **`_data/navigation.yml`** — Masthead navigation menu configuration.
* **`projects/`**
  * **`index.md`** — All-projects portfolio gallery categorized by domain.
  * **`teleop.md`** — Robotic Arm & Hand Teleoperation case study.
  * **`rover.md`** — Autonomous Mapping Rover (Mecanum AMR) case study.
  * **`pick_and_place.md`** — Vision-Guided Pick and Place robot case study.
  * **`mock_driving_simulator.md`** — Automotive HIL driving simulator case study.
* **`about.md`** — Biography, engineering philosophy, laboratory toolset, and educational background.
* **`resume.md`** — Interactive curriculum vitae with experience timeline and skills matrix.
* **`contact.md`** — Direct contact methods and inquiry message form.
* **`assets/`**
  * **`css/main.scss`** — Custom modern styling, badges, card hover effects, and responsive utilities.
  * **`images/`** — Project imagery, hardware photos, and avatar graphics.

---

## ✏️ Quick Customization Checklist

All placeholder sections are marked so you can easily update them with your exact details:

1. **Personal Information (`_config.yml`):**
   * Update your email address and LinkedIn URL in `author.links` and `footer.links`.
   * Update your bio or location if desired.
2. **Resume & Experience (`resume.md`):**
   * Update your university name, graduation date, and specific job titles/dates in the timeline.
3. **Contact Form (`contact.md`):**
   * Replace `https://formspree.io/f/placeholder` with your free [Formspree](https://formspree.io) endpoint, or swap with your direct email.
4. **Project Links:**
   * Add any GitHub repository or demo video links into the project case studies.

---

## 🛠 Local Development (Optional)

GitHub Pages automatically builds and deploys this repository upon pushing to the `main` branch. 

To preview changes locally with Ruby & Bundler:
```bash
bundle install
bundle exec jekyll serve
```
Then visit `http://localhost:4000`.