# jspsych-button-response-audio

(Note: this is based heavily on Josh de Leeuw's jsPsych button response plugin [markdown docs](https://github.com/jspsych/jsPsych/tree/master/docs/markdown_docs/plugins), adapted for a custom audio version by Becky Gilbert.)

This plugin plays a sound file (via the WebAudio API) and allows the subject to respond by pressing a button on the screen. The trial can be set to a fixed duration, or it can end automatically after the audio ends or after a response is made. Responses can be prevented or allowed while the sound is playing. The button itself can be customized using HTML formatting.

## Parameters

This table lists the parameters associated with this plugin. Parameters with a default value of *undefined* must be specified. Other parameters can be left unspecified if the default value is acceptable.

Parameter | Type | Default Value | Description
----------|------|---------------|------------
stimulus | string | *undefined* | Path to an audio file.
choices | array | [ ] | This array contains the labels for the buttons.
button_html | string or array | `'<button class="jspsych-btn">%choice%</button>'` | A template of HTML for generating the button elements. You can override this to create customized buttons of various kinds. The string `%choice%` will be changed to the corresponding element of the `choices` array. You may also specify an array of strings, if you need different HTML to render for each button. If you do specify an array, the `choices` array and this array must have the same length. The HTML from position 0 in the `button_html` array will be used to create the button for element 0 in the `choices` array, and so on.
prompt | string | "" | This string can contain HTML markup. Any content here will be displayed below the stimulus. The intention is that it can be used to provide a reminder about the task or trial (e.g. what the question is).
timing_response | numeric | -1 | How long to wait for the subject to make a response before ending the trial in milliseconds. If the subject fails to make a response before this timer is reached, the subject's response will be recorded as -1 for the trial and the trial will end. If the value of this parameter is -1, the trial will wait for a response indefinitely.
response_ends_trial | boolean | true | If true, then the trial will end whenever the subject makes a response (assuming they make their response before the cutoff specified by the `timing_response` parameter). If false, then the trial will continue until the value for `timing_response` is reached. You can use this parameter to force the subject to listen to the whole stimulus and/or control the trial duration, even if they respond before the time is complete.
trial_ends_after_audio | boolean | false | Should the trial end automatically after the audio stops playing? This could be used with `ignore_responses_during_audio` set to false to allow button presses during the audio playback, using the audio duration as the response time limit.
ignore_responses_during_audio | boolean | false | Should responses be ignored during the audio playback? Default is false (buttons are active/clickable while sound is playing). If true, the buttons will be disabled until the sound stops playing.

## Data Generated

In addition to the default data collected by all jsPsych plugins, this plugin collects the following data for each trial.

Name | Type | Value
-----|------|------
stimulus | string | The path to the audio stimulus.
button_pressed | numeric | Indicates which button the subject pressed. The first button in the `choices` array is 0, the second is 1, and so on. If there was no response then the value will be -1.
rt | numeric | The response time in milliseconds, measured from the start of the trial until the subject makes a response. If there was no response then this value will be -1.
rt_audio | numeric | The response time in milliseconds from the start of the audio playback until a response is made. This is to account for the possibility of an audio loading/playback delay at the start of the trial. If the audio plays with WebAudio API then this variable uses the audio context timing. If the audio falls back to HTML5 due to browser compatibility, this is based on Date.now(). If there was no response then this value will be -1.
audio_ended | boolean | Whether or not the audio finished playing. The audio may stop early if e.g. a response can be made while the audio is playing and the trial is set to end on a response.
audio_duration | numeric | If the audio finished playing, this will be the recorded duration of the audio playback in  milliseconds (using either the WebAudio API context timing or Date.now). If the audio didn't finish playing then this will be -1.
rt_from_audio_end | numeric | Response time relative to the audio end time, rather than the trial start time (`rt`) or the audio start time (`rt_audio`). This will be a negative value if a response was made before the audio ended, and positive if the response was made after the audio ended. If the audio did not finish playing, or if a response was not made before the trial time limit, then this will be -1.

## Examples

#### Play an audio file and wait until a button response is made

```javascript
var trial = {
    type: 'button-response-audio',
    stimulus: 'audio/sentence1.mp3',
    choices: ['Yes', 'No'],
    prompt: "<p class='center-content'>Does this sentence make sense?</p>"
};
```