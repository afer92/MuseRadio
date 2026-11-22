# 📻 Raspiaudio Radio — Firmware ESPHome (Music Assistant, Voice Assistant & Web Radio autonome)
 
![Aperçu de l'écran Raspiaudio Radio](docs/screen.jpg)

Configuration **ESPHome** complète et optimisée pour la carte **Raspiaudio Radio (ESP32-S3)** intégrant simultanément :
* **Music Assistant (via Sendspin)** : lecture multiroom FLAC 48 kHz avec affichage des métadonnées et contrôle du volume de groupe.
* **Home Assistant Voice Assistant & microWakeWord** : détection locale des mots de réveil (*Okay Nabu*, *Hey Jarvis*, *Hey Mycroft*, *Stop*) avec bascule intelligente du bus I2S partagé.
* **Radio Web locale autonome (MP3)** : écoute directe de flux radio Icecast/HTTP sans dépendre de Home Assistant ni de Music Assistant.
* **Interface graphique événementielle (ST7789V 320×240)** : affichage haute performance (`update_interval: never`) avec météo Home Assistant (température + icône vectorielle), jauge batterie, signal Wi-Fi et retour visuel des actions.

---

## ✨ Fonctionnalités principales

### 1. Double mode d'écoute Radio
* **Mode Music Assistant (Boutons 1, 2 et 3)** :
  * **Appui court (< 1 s)** : lance la station mémorisée sur le lecteur Music Assistant configuré (`ma_player_entity_id`).
  * **Appui long (≥ 1,8 s)** : récupère l'URI de la station en cours via `music_assistant.get_queue` et l'enregistre dans la mémoire flash du bouton.
* **Mode Web Radio directe autonome (Bouton 4 + Encodeur Station)** :
  * **Encodeur rotatif gauche (`GPIO6` / `GPIO7`)** : permet de parcourir la liste déroulante des stations locales (`local_radio_select`) avec aperçu immédiat à l'écran et bascule automatique temporisée (`600 ms`) si la lecture est déjà active.
  * **Appui court sur le Bouton 4** : démarre ou arrête le flux MP3 direct sélectionné (ex. *France Info*, *France Inter*, *France Musique*, *FIP*, *Nostalgie*, *Radio Classique*), en coupant automatiquement Music Assistant si nécessaire.

### 2. Assistant Vocal intelligent (microWakeWord + Assist)
* **Détection locale** : prise en charge des modèles `okay_nabu`, `hey_jarvis`, `hey_mycroft` et `stop` avec réglage de sensibilité à la volée depuis Home Assistant (*Slightly*, *Moderately*, *Very sensitive*).
* **Interruption temporaire de la musique (`temporary_assist`)** : un appui sur le bouton **Assist (`GPIO45`)** ou la télécommande IR met en pause le groupe Sendspin, libère le bus I2S partagé, écoute la commande vocale, puis relance automatiquement la musique.

### 3. Gestion matérielle avancée
* **Codec Audio ES8388** :
  * Détection automatique de prise casque (`GPIO10`) avec commutation matérielle des registres I2C entre haut-parleur interne et sortie ligne/casque.
  * Courbe de gain fine sur les registres `0x2E`–`0x31` pour un réglage précis à très bas volume (`< 10 %`).
* **Alimentation & Batterie** : mesure de tension (`GPIO13`) convertie en pourcentage via une courbe polynomiale à 22 points et détection de charge USB (`GPIO21`).
* **Télécommande Infrarouge (`GPIO47`, protocole NEC)** : volume +/-, Mute/Unmute et déclenchement de l'assistant vocal.

---

## 🎛️ Commandes physiques

| Contrôle | Broche(s) | Action courte | Action longue / Rotation |
| :--- | :--- | :--- | :--- |
| **Encodeur Volume** | `GPIO42` / `GPIO3` | — | Ajuste le volume (groupe MA ou volume local) |
| **Clic Encodeur Volume** | `GPIO48` | Mute / Unmute | — |
| **Encodeur Fonction** | `GPIO7` / `GPIO6` | — | Sélectionne la station dans la liste Web Radio |
| **Clic Encodeur Fonction** | `GPIO45` | Lance / interrompt **Assist** | — |
| **Bouton 1** | `GPIO1` (ADC) | Joue le **Preset MA 1** | Mémorise la station MA en cours sur **Preset 1** |
| **Bouton 2** | `GPIO1` (ADC) | Joue le **Preset MA 2** | Mémorise la station MA en cours sur **Preset 2** |
| **Bouton 3** | `GPIO1` (ADC) | Joue le **Preset MA 3** | Mémorise la station MA en cours sur **Preset 3** |
| **Bouton 4 (Confirm)** | `GPIO1` (ADC) | Démarre / Arrête la **Web Radio locale** | — |

---

## 🛠️ Installation et prérequis

### 1. Prérequis logiciels
* **ESPHome** : version `2026.9.0` ou supérieure (framework `esp-idf`).
* **Home Assistant** avec :
  * L'intégration **Music Assistant** (et le composant **Sendspin**).
  * Une entité météo (par défaut `weather.forecast_maison`, modifiable via l'ancre `.variables` en haut du fichier YAML).

### 2. Arborescence des fichiers requise
Assurez-vous que les dossiers suivants sont présents dans votre répertoire `/config/esphome/` avant de compiler :

```text
/config/esphome/
├── Raspiaudio_Dev_a998c4.yaml
├── secrets.yaml
├── common/
│   └── base_text_sensor.yaml
└── images/
    ├── Raspiaudio.png
    ├── WFWW.png
    ├── W.png
    ├── LISTEN.png
    ├── ANSWER.png
    ├── mute_mic.png
    └── mute_speaker.png
```

### 3. Configuration de `secrets.yaml`
Renseignez vos identifiants Wi-Fi dans votre fichier `secrets.yaml` :

```yaml
wifi_ssid: "Votre_SSID_Principal"
wifi_ssid_1: "Votre_SSID_Secondaire"
wifi_password: "Votre_Mot_De_Passe_WiFi"
```

---

## ⚙️ Paramétrage dans Home Assistant

Une fois l'appareil flashé et ajouté dans **Paramètres → Appareils et services → ESPHome**, vous disposez des contrôles suivants :
* **Web Radio Station** (`select`) : choisissez la station jouée en direct par le bouton 4.
* **Wake word sensitivity** (`select`) : ajustez la sensibilité du micro de réveil.
* **Station button 1 à 3** (`text`) : visualisez ou modifiez manuellement les URI (`library://radio/...` ou `http://...`) des boutons Music Assistant.
* **set_ma_player_entity_id** (`text`) : forcez l'identifiant d'entité Home Assistant du lecteur Music Assistant (ex. `media_player.raspiaudio_radio`) si la détection automatique n'est pas souhaitée.

---

## 🏗️ Architecture technique (Brochage ESP32-S3)

* **Microcontrôleur** : `esp32-s3-devkitc-1` (Flash 8 Mo, PSRAM Octal 80 MHz, CPU 240 MHz)
* **Bus I2C (Codec ES8388 @ `0x10`)** : `SDA = GPIO18`, `SCL = GPIO11`
* **Bus I2S Audio** : `MCLK = GPIO0`, `BCLK = GPIO5`, `LRCLK = GPIO16`, `DOUT = GPIO17`, `DIN = GPIO4`, `DAC_MUTE = GPIO46`
* **Écran SPI (ST7789V 320×240)** : `CLK = GPIO12`, `MOSI = GPIO8`, `CS = GPIO40`, `DC = GPIO39`, `RST = GPIO9`, `Backlight = GPIO41`

---

## 🩺 Dépannage (FAQ)

### 1. Erreur de compilation : `'logoP' was not declared in this scope`
* **Cause** : L'appel C++ `id(myDisplay).show_page(logoP)` dans `on_boot:` est évalué avant que les identifiants de pages de `display:` ne soient déclarés, ou une erreur d'indentation empêche la génération de la section `display:`.
* **Solution** : Utilisez l'action native YAML `- display.page.show: logoP` suivie de `- component.update: myDisplay` dans `on_boot:`, et vérifiez qu'il n'existe qu'un seul bloc `display:` et un seul bloc `select:` de premier niveau dans votre fichier.

### 2. La Web Radio locale (Bouton 4) ne démarre pas ou le son reste bloqué
* **Cause** : Le bus audio `music_speaker` est encore verrouillé par **Music Assistant (Sendspin)** ou le lecteur local `local_radio_player` n'a pas fermé son flux HTTP précédent.
* **Solution** :
  * Vérifiez que `toggle_local_radio` arrête bien `music_source_player` et attend `4s` avant de lancer `local_radio_player`.
  * Si un flux Icecast reste figé après une coupure réseau, appuyez une fois sur le **Bouton 4** pour forcer `media_player.stop` (`STOP RADIO`), attendez 1 seconde, puis appuyez de nouveau pour relancer la connexion.

### 3. Le volume sonore est trop fort au démarrage ou au lancement de la Web Radio
* **Cause** : Par défaut, un composant `media_player` sous ESPHome initialise son volume logiciel à `1.0` (100 %).
* **Solution** :
  * Assurez-vous que `volume_initial: 0.3` est bien défini sur `local_radio_player`.
  * Dans le script `logo`, conservez la limitation temporaire `std::min(id(Vol), 0.30f)` avant la lecture de `little_sound`.

### 4. L'assistant vocal ne se déclenche pas à la voix (*Okay Nabu*) pendant la musique
* **Comportement normal** : Pour éviter que le haut-parleur n'auto-déclenche le micro et pour préserver les ressources CPU de l'ESP32-S3 pendant le décodage FLAC 48 kHz, `micro_wake_word` est volontairement mis en pause lorsque `music_active` est vrai.
* **Utilisation pendant la musique** : Appuyez sur le **bouton Assist (`GPIO45`)** ou sur la touche **Retour** de la télécommande IR. Le script `temporary_assist` mettra la musique en pause, libérera le bus I2S, écoutera votre demande puis relancera automatiquement la lecture.

### 5. L'appui long sur les boutons 1 à 3 affiche `"NO RADIO"` ou `"PLAY FAILED"`
* **Cause** : L'entité Music Assistant (`ma_player_entity_id`) n'est pas encore connue de l'ESP32 ou aucune file de lecture active n'est détectée dans Home Assistant.
* **Solution** :
  1. Lancez une première fois une radio depuis l'interface Home Assistant sur votre lecteur Raspiaudio.
  2. Maintenez le **Bouton 1, 2 ou 3** appuyé pendant **2 secondes** jusqu'à l'affichage de `"MEMO X OK"` : l'appareil enregistrera automatiquement l'identifiant du lecteur et l'URI du flux.
  3. Vous pouvez aussi saisir manuellement l'entité (ex. `media_player.raspiaudio_radio`) dans le champ **set_ma_player_entity_id** de l'appareil sous Home Assistant.

### 6. L'encodeur de sélection de stations saute 2 stations par cran
* **Cause** : Le paramètre `resolution` de `function_encoder` est réglé sur `2` au lieu de `1`.
* **Solution** : Réglez `resolution: 1` dans la configuration `rotary_encoder` de `function_encoder` (`GPIO7` / `GPIO6`).

### 7. La température ou l'icône météo affiche `--°C` dans l'en-tête
* **Cause** : L'entité météo configurée dans `.variables` (`&weather_entity "weather.forecast_maison"`) n'existe pas sous ce nom dans votre installation Home Assistant, ou l'ESP32 n'est pas encore connecté à l'API Home Assistant.
* **Solution** : Remplacez `"weather.forecast_maison"` en haut du fichier YAML par l'identifiant exact de votre entité météo Home Assistant (ex. `"weather.maison"` ou `"weather.openweathermap"`).

### 8. L'écran reste noir ou bloqué sur `"Waiting WiFi..."`
* **Écran noir après 1 heure** : Le rétroéclairage s'éteint automatiquement après `3600 s` d'inactivité (`lightTO`). Tournez n'importe quelle molette ou appuyez sur un bouton pour le réveiller.
* **Bloqué sur `"Waiting WiFi..."`** : Vérifiez les identifiants dans `secrets.yaml`. En cas d'absence prolongée de réseau, l'appareil active son point d'accès de secours **`Raspiaudio-radio-full`** (mot de passe : `12345678`) permettant de reconfigurer le Wi-Fi via le portail captif.

