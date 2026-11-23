# 🤝 Contributing to MuseRadio / Contribuer à MuseRadio

🌐 **English** | **Français**

---

## 🇬🇧 English

Thank you for your interest in improving **MuseRadio**! Contributions of all kinds are welcome—whether it's adding new web radio streams, improving the ESPHome YAML configuration, fixing bugs, or enhancing documentation.

### 1. How to Contribute

* **Report a Bug:** Open an [Issue](https://github.com) describing the problem, your ESPHome version, and relevant serial/API logs.
* **Suggest a Feature or Station:** Open an issue to discuss new ideas before submitting large changes.
* **Submit a Pull Request (PR):**
  1. Fork the repository.
  2. Create a new branch (`git checkout -b feature/my-improvement`).
  3. Make your changes and test them on your **Raspiaudio Radio (ESP32-S3)** hardware.
  4. Commit your changes with clear messages and open a Pull Request.

### 2. Adding or Updating Web Radio Stations

If you want to suggest updates to `stations/stations.yaml`:
* Ensure the stream URL uses a direct **MP3** or **AAC** HTTP/HTTPS Icecast stream supported by ESPHome's `media_player` component (avoid `.m3u` or `.pls` playlist wrappers if possible).
* Keep the station list numbering consistent with the `local_radio_select` options (slots `1` to `9`, keeping slot `10` reserved for the custom user URL).

### 3. Security & Code Guidelines

* **Never commit real credentials:** Do not include your personal Wi-Fi SSIDs, passwords, or API keys in `secrets.yaml` or `Raspiaudio.yaml`.
* **ESPHome Validation:** Before submitting a PR, please verify that your configuration compiles cleanly using:
  ```bash
  esphome config Raspiaudio.yaml
  ```

---

## 🇫🇷 Français

Merci pour votre intérêt et votre aide pour améliorer **MuseRadio** ! Toutes les contributions sont les bienvenues : ajout de flux web radio, optimisation de la configuration ESPHome, correction de bugs ou amélioration de la documentation.

### 1. Comment contribuer

* **Signaler un bug :** Ouvrez une [Issue](https://github.com) en décrivant le problème rencontré, votre version d'ESPHome ainsi que les journaux (logs) pertinents.
* **Proposer une fonctionnalité :** Ouvrez une issue pour discuter de votre idée avant de développer des modifications importantes.
* **Soumettre une Pull Request (PR) :**
  1. Faites un *Fork* du dépôt.
  2. Créez une branche dédiée (`git checkout -b feature/mon-amelioration`).
  3. Effectuez vos modifications et testez-les sur votre carte **Raspiaudio Radio (ESP32-S3)**.
  4. Envoyez vos commits avec des messages clairs et ouvrez une *Pull Request*.

### 2. Ajouter ou modifier des stations Web Radio

Si vous proposez des modifications dans `stations/stations.yaml` :
* Utilisez de préférence un flux direct **MP3** ou **AAC** (HTTP/HTTPS Icecast) directement compatible avec le composant `media_player` d'ESPHome (évitez les liens de listes de lecture `.m3u` ou `.pls`).
* Respectez la numérotation des emplacements dans `local_radio_select` (emplacements `1` à `9`, l'emplacement `10` restant réservé à l'URL personnalisée de l'utilisateur).

### 3. Sécurité et bonnes pratiques

* **Ne publiez jamais vos identifiants :** Veillez à ne jamais inclure vos vrais identifiants Wi-Fi ou clés API dans `secrets.yaml` ou `Raspiaudio.yaml`.
* **Validation ESPHome :** Avant de soumettre une *Pull Request*, vérifiez que la configuration compile sans erreur avec la commande :
  ```bash
  esphome config Raspiaudio.yaml
  ```
