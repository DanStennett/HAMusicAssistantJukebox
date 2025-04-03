# HAMusicAssistantJukebox

A web-based song request system that integrates with Home Assistant and Music Assistant to create an interactive jukebox experience for guests!

![alt text](https://github.com/DanStennett/HAMusicAssistantJukebox/blob/main/readme_image.jpg?raw=true)

https://github.com/user-attachments/assets/bea6223d-663b-40de-b3ec-5f62a9460696


## Features

- Real-time song search across all connected providers
- No login, just pass out the url to guests and enjoy!
- Queue songs with your guests
- Auto queues a nominated default party playlist when jukebox requests are all played to keep the party going
- Minimalist responsive design with album artwork display
- Access control through Home Assistant
- Auto revoking/creating access keys when enabling/disabling the service

## Prerequisites

- Home Assistant instance with Music Assistant integration
- A supported music provider configured in Music Assistant (e.g. Spotify, Apple Music)
- Media player entity configured in Home Assistant
- A default party playlist configured in MA for when the request queue is empty.

## Getting Started

### 1. Getting Required Values

1. **Home Assistant URL** (`HAURL`):
   - Your Home Assistant instance URL (highly advise limiting this to your internal network only)
   - Example: `http://homeassistant.local:8123`

2. **Music Assistant Config ID** (`MUSIC_ASSISTANT_CONFIG`):
   - In Home Assistant, go to Developer Tools > Services
   - Find `music_assistant.search`
   - Look for `config_entry_id` in the service data
   - Copy the ID value

3. **Media Player Entity** (`MEDIA_PLAYER`):
   - In Home Assistant, go to Developer Tools > States
   - Find your speaker's entity ID
   - Example: `media_player.party_speaker`

4. **Webhook ID** (`QUEUEWEBHOOK`):
   - Required to let home assistant know which queuing type to choose
   - Create an automation with webhook trigger (See step 3 of Config & Setup)
   - Save the automation
   - Copy the webhook ID from the trigger

## 2. Configuration and Setup

Create these helpers in Home Assistant:

```yaml
input_boolean:
  songrequestaccess:
    name: Song Request Access
    initial: off
  jukebox_queue:
    name: Jukebox Queue Mode
    initial: on
input_number:
  jukebox_queue_length:
    name: Jukebox Queue Length
    initial: 0
```

1. **Set up API Token Management** 
   - Go to your Home Assistant Admin profile
   - Scroll to bottom under Long Lived Access Tokens
   - Click "Create Token" (this will be your management token and only used internally)
   - Copy the generated token and paste it into the manage_tokens.py script in the parameter at the top of the script
   - Enter your HA local IP at the top of the script
   - Save the file
   - Copy the saved manage_tokens.py file to Config/python_scripts/manage_tokens.py
   - open your Configuration.yaml and add these entries to your file:
     ```yaml
     shell_command:
        create_jukebox_token: python3 /config/python_scripts/manage_tokens.py 
        delete_jukebox_token: python3 /config/python_scripts/manage_tokens.py delete //revokes they key and deletes the key file from config/www
     ```
     The create_jukebox_token function generates a new token and puts it in a .key file in the config/www directory for use by the app.
     The delete_jukebox_token function deletes the token, revoking access and deletes the key file from the config/www directory.
   - Save your Configuration.yaml and restart home assistant.

2. **Set up the Disable/Enable Jukebox Automation**
   - Create a new home assistant automation using the Jukebox - Toggle Jukebox Access Access.yaml code
   - Save the automation

3. **Set up the Notify of Queued Song Automation**
   - Create a new home assistant automation using the Jukebox - Notify of Queued Song.yaml code
   - Replace the <YOUR WEBHOOK ID HERE> code with your own generated from the webhook trigger of the automation.
   - Save the automation
     
4. **Set up the set Default playlist when queue reaches zero Automation**
   - Create a new home assistant automation using the Jukebox - Set Default Playlist when Jukebox Queue is Zero.yaml code
   - Replace the <YOUR DEFAULT PARTY PLAYLIST HERE> with the name of your chosen default party playlist in music assistant.
   - Replace the <YOUR MEDIA PLAYER HERE> with the entity name of your chosen Music Assistant media player
   - Save the automation
     
5. **Set up the Track Queue Size Automation**
   - Create a new home assistant automation using the Jukebox - Track Queue Size.yaml code 
   - Replace the <YOUR MEDIA PLAYER HERE> with your chosen Music Assistant media player from the previous steps
   - Save the automation.


6. **HTML Configuration Values**

Update the configuration section in `jukebox.html`:

```javascript
const HAURL = 'http://<your HA IP here>:8123'; // Your internal HA URL IP
const QUEUEWEBHOOK = 'your-webhook-id'; // required to coordinate Jukebox Queue mode.
const MEDIA_PLAYER = "media_player.your_speaker"; // Your speaker entity to play the tuuuuuuunes
const MUSIC_ASSISTANT_CONFIG = "your_music_assistant_config_id"; // Your MA config ID
```

7. **Publish the HTML**
   - Place the configured HTML file to your config\www folder in Home Assistant along with the bg.jpg

   - Test the url by switching on the input_boolean.songrequestaccess input boolean toggle and visiting the path in your browser.
      It should be something like http://192.168.1.34:8123/local/jukebox.html and the interface should appear.

## Usage Control

- Toggle `input_boolean.songrequestaccess` to enable/disable the interface and revoke/create access token.

## Additional enhancements:
I stream my music to a couple of android tv's using Chomecast around the house with MA.

Using PiPup on an android tv, you can display the QR code as a Picture-In-Picture pop up over your MA music player and trigger 
it to appear using the songrequestaccess switch helper created earlier so guests can easily Scan the QR
Code from your TV(s) when the request systems is active.

Guide to set up PiPUp on android tv's is here:
https://community.home-assistant.io/t/a-short-guide-for-setting-up-tv-pip-notifications-with-pipup/537084

## Troubleshooting

If you encounter issues:

1. Check browser console for errors
2. Verify all configuration values are correct
3. Ensure Home Assistant is accessible
4. Confirm Music Assistant is properly configured
5. Verify the media player entity is available

## Relevant Support Materials

- Home Assistant: [Community Forums](https://community.home-assistant.io/)
- Music Assistant: [Documentation](https://music-assistant.github.io/)
- Issues with this app: Open an issue on the repository
