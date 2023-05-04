# Send the Voicemail with a transcription with OpenAI Wisper
Next we show the steps to follow to send the Voicemail with the transcription of the content. There are two ways to do it, using the Script or doing it step by step.

## Prerequisites
- Have installed VitalPBX 4 and configured the sending of Email. This is done in ADMIN/System Settings/Email Settings.
- Create an OpenAI account.
- Create OpenAI API Key
- Have root access to the VitalPBX Server

## Instalation
Download the sendmail-gcloud file and copy it to /usr/sbin/
<pre>
wget https://raw.githubusercontent.com/VitalPBX/vitalpbx-voicemail-transcription-openai/main/sendmail-openai /usr/sbin/
</pre>

Edit the file /usr/sbin/sendmail-openai and replace in line 74 the TOKEN with the API Key that OpenAI gives us.
<pre>
nano /usr.sbin/sendmail-openai
</pre>

<pre>
RESULT=`curl --request POST --url https://api.openai.com/v1/audio/transcriptions --header 'Authorization: Bearer TOKEN' --header 'Content-Type: multipart/form-data' --form file=@stream.part3.mp3 --form response_format=text --form model=whisper-1`
</pre>

Later we create the file voicemail__60-general.conf in /etc/Asterisk/vitalpbx/ with the following content.
<pre>
nano /etc/asterisk/vitalpbx/voicemail__60-general.conf 
</pre>

Content
<pre>
[general](+)
;You override the default program to send e-mail to use the script
mailcmd=/usr/sbin/sendmail-openai
</pre>

Now we assign the corresponding permissions
<pre>
cd /usr/sbin/
chown asterisk:asterisk sendmail-openai
chmod 744 sendmail-openai
</pre>

Finally we do a reload of the voicemail module
<pre>
asterisk -rx"module reload app_voicemail.so"
</pre>

Overall, voicemail transcription can be a useful tool for anyone who receives voicemails regularly, and it can provide several benefits, including time savings, improved accessibility, increased accuracy, and convenience.

For more details about the implementation of Google Cloud VoiceMail Transcription, visit the following Blog:

https://vitalpbx.com/blog/vitalpbx-voicemail-transcription-with-openai/
