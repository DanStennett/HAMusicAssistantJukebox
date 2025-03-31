# HAMusicAssistantJukebox

A web-based song request system that integrates with Home Assistant and Music Assistant to create an interactive jukebox experience for guests!

![alt text](https://github.com/DanStennett/HAMusicAssistantJukebox/blob/main/readme_image.jpg?raw=true)

https://github.com/user-attachments/assets/bea6223d-663b-40de-b3ec-5f62a9460696


## Features

- Real-time song search across all connected providers
- No login, just pass out the url to guests and enjoy!
- Queue songs with your guests
- Auto queues a nominated default party playlist when jukebox requests are all played to keep the party going
- Mobile-responsive design with album artwork display
- Access control through Home Assistant

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

2. **Long-Lived Access Token** (`API_TOKEN`):
   - Go to your Home Assistant profile
   - Scroll to bottom under Long Lived Access Tokens
   - Click "Create Token"
   - Copy the generated token

3. **Music Assistant Config ID** (`MUSIC_ASSISTANT_CONFIG`):
   - In Home Assistant, go to Developer Tools > Services
   - Find `music_assistant.search`
   - Look for `config_entry_id` in the service data
   - Copy the ID value

4. **Media Player Entity** (`MEDIA_PLAYER`):
   - In Home Assistant, go to Developer Tools > States
   - Find your speaker's entity ID
   - Example: `media_player.party_speaker`

5. **Webhook ID** (`QUEUEWEBHOOK`):
   - Required to let home assistant know which queuing type to choose
   - Create an automation with webhook trigger
   - Save the automation (will set this up later)
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

1. **Set up the Notify of Queued Song Automation**
   - Create a new home assistant automation using the Jukebox - Notify of Queued Song.yaml code
   - replace the <YOUR WEBHOOK ID HERE> code with your own generated from the webhook trigger.
   - Save the automation
     
2. **Set up the set Default playlist when queue reaches zero Automation**
   - Create a new home assistant automation using the Jukebox - Set Default Playlist when Jukebox Queue is Zero.yaml code
   - Replace the <YOUR DEFAULT PARTY PLAYLIST HERE> with the name of your chosen default party playlist in music assistant.
   - Replace the <YOUR MEDIA PLAYER HERE> with the entity name of your chosen Music Assistant media player
   - Save the automation
     
3. **Set up the Track Queue Size Automation**
   - Create a new home assistant automation using the Jukebox - Track Queue Size.yaml code 
   - Replace the <YOUR MEDIA PLAYER HERE> with your chosen Music Assistant media player from the previous steps
   - Save the automation.


4. **HTML Configuration Values**

Update the configuration section in `SongRequest.html`:

```javascript
const HAURL = 'http://<your HA IP here>:8123'; // Your internal HA URL IP
const API_TOKEN = 'your_long_lived_access_token'; // Your HA token
const QUEUEWEBHOOK = 'your-webhook-id'; // required to coordinate Jukebox Queue mode.
const MEDIA_PLAYER = "media_player.your_speaker"; // Your speaker entity to play the tuuuuuuunes
const MUSIC_ASSISTANT_CONFIG = "your_music_assistant_config_id"; // Your MA config ID
```

5. **Publish the HTML**
   - Place the configured HTML file to your config\www folder in Home Assistant along with the bg.jpg

   - Test the url by visiting the path in your browser.
      It should be something like http://192.168.1.34:8123/local/jukebox.html and the interface should appear.

## Usage Control

- Toggle `input_boolean.songrequestaccess` to enable/disable the interface.

## Tips:
Create a QR Code of your URL to share with guests so they can easily access it

## Additional enhancements:
I stream my music to a couple of android tv's around the house with MA.

Using PiPup on an android tv, you can display the QR code as a PiP pop up over your MA music and trigger 
it to appear using the songrequestaccess switch helper created earlier so guests can easily Scan the QR
Code from your TV(s) when the request systems is active.

Guide to set up PiPUp on android tv's is here:
https://community.home-assistant.io/t/a-short-guide-for-setting-up-tv-pip-notifications-with-pipup/537084


## Security Considerations

- The token is in the html which could be cached on participents devices which could technically be
  used to access your home assistant if your friends are tech savvy enough to dig it out.
  Open to suggestions on how to better manage this but for now, id recommend rotating your token regularly.
- Consider adding authentication if hosting publicly

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
