---
layout: post
title: "Whisper Simple Dictation: A Powerful Open-Source Speech-to-Text Tool"
date: 2024-09-14
categories:
  - tools
  - speech-to-text
  - open-source
---

# Exploring Whisper Simple Dictation

In the world of speech-to-text technology, a new open-source tool has caught my attention: [Whisper Simple Dictation](https://github.com/guidov/whisper-simple-dictation). This powerful and user-friendly application leverages OpenAI's Whisper model to provide accurate and efficient speech recognition capabilities.
This is a fork of: https://github.com/filyp/whisper-simple-dictation.git

## Key Features

1. **Easy Setup**: The tool is designed for simplicity, making it accessible even for those with limited technical expertise.
2. **Real-time Transcription**: It offers real-time speech-to-text conversion, allowing for immediate feedback and editing.
3. **Multiple Language Support**: Thanks to Whisper's multilingual capabilities, it can handle a wide range of languages.
4. **Customizable Shortcuts**: Users can define their own keyboard shortcuts for various functions, enhancing productivity.
5. **Open-Source**: Being open-source, it's free to use and can be modified to suit specific needs.

## How It Works

Whisper Simple Dictation uses your computer's microphone to capture audio. It then processes this audio in chunks using the Whisper model, converting speech to text in real-time. The transcribed text is displayed in a simple interface where you can edit and copy it as needed.

## Installation and Getting Started

To use Whisper Simple Dictation, follow these steps:

1. Clone the GitHub repository and navigate to the project directory:
   ```bash
   git clone https://github.com/guidov/whisper-simple-dictation.git
   cd whisper-simple-dictation
   ```

2. Create a virtual environment:
   ```bash
   python3 -m venv venv --copies
   ```

3. Activate the virtual environment:
   ```bash
   source venv/bin/activate  # On Unix or MacOS
   # Or
   venv\Scripts\activate  # On Windows
   ```

4. Install the required dependencies:
   ```bash
   venv/bin/python -m pip install -r requirements_remote.txt
   ```

5. Run the application:
   ```bash
   bash run_dictation_remote.sh en
   ```

For more detailed instructions and troubleshooting, visit the [project's GitHub page](https://github.com/guidov/whisper-simple-dictation).

Note: Ensure you have Python 3.7 or later installed on your system before starting the installation process.

## Potential Applications

This tool can be incredibly useful for:
- Writers and journalists for quick note-taking
- Students transcribing lectures
- Professionals conducting interviews
- Anyone looking to convert their spoken words into text efficiently

## Conclusion

Whisper Simple Dictation represents a significant step forward in making advanced speech-to-text technology accessible to everyone. Its combination of accuracy, ease of use, and open-source nature makes it a valuable tool for a wide range of users. Whether you're a professional writer, a student, or just someone looking to streamline your workflow, this tool is definitely worth checking out.

Give it a try and experience the power of open-source speech recognition!

## Setting Up a Systemd Service for Remote Connection

To make Whisper Simple Dictation even more convenient, you can set up a systemd service for remote connection. Here's how I've done it:

1. I created a service file at `~/.config/systemd/user/whisper-dictation.service` with the following content:

```
[Unit]
Description=Whisper Dictation Service

[Service]
ExecStart=/bin/bash /home/guido/whisper-simple-dictation/run_dictation_remote.sh en
StandardOutput=file:/home/guido/logs/whisper-dictation.log
StandardError=file:/home/guido/logs/whisper-dictation.log
Restart=always

[Install]
WantedBy=default.target
```

2. To manage the service, use the following commands:
   - Start the service: `systemctl --user start whisper-dictation.service`
   - Stop the service: `systemctl --user stop whisper-dictation.service`
   - Restart the service: `systemctl --user restart whisper-dictation.service`
   - Check the status: `systemctl --user status whisper-dictation.service`
   - Enable the service to start on boot: `systemctl --user enable whisper-dictation.service`

3. After making changes to the service file, reload the systemd daemon and start the service:
   ```
   systemctl daemon-reload
   systemctl --user start whisper-dictation.service
   systemctl --user status whisper-dictation.service
   ```

This setup allows for a seamless integration of the tool into your workflow. The service will automatically restart if it crashes, and it logs both standard output and errors to a file for easy troubleshooting.

## OpenAI API Key and Usage

To use Whisper Simple Dictation, you'll need to set up your OpenAI API key. Make sure to keep this key secure and never share it publicly.

To add your OpenAI API key, you can use the following command:

```bash
echo sk-your_api_key_here > ~/.config/openai.token
```

Replace `sk-your_api_key_here` with your actual OpenAI API key. This command will create or overwrite the `openai.token` file in your `~/.config/` directory with your API key.

## User Experience

In my experience, the tool is quite fast and accurate. It uses the right control key to transcribe speech to text (STT). While it doesn't work in GNOME terminals (stay tuned for a future post on this), it integrates well with code development environments like Microsoft Visual Studio Code ("code") and Cursor AI ("cursor"). Waveterm is great also

The speed and accuracy of the transcription have significantly improved my productivity, especially when working on coding projects or writing documentation.

Remember, the power of open-source tools like Whisper Simple Dictation lies not just in their functionality, but also in the ability to customize and integrate them into your unique workflow. Happy dictating!

