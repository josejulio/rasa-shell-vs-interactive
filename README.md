# Rasa shell vs interactive

This project reproduces an issue saw on Rasa version `3.5.10`  and `3.6.13`. Unsure about any other version.

The issue is tracked as [OSS-743](https://rasa-open-source.atlassian.net/browse/OSS-743).

## Getting started

This project uses [pipenv](https://pipenv.pypa.io/en/latest/) to manage its dependencies 
(only [rasa](https://github.com/rasahq/rasa/)).
Assuming `pipenv` is already installed, running the following should install `rasa`:

```bash
pipenv install
```

## How to reproduce the issue

The difference I saw in `shell` vs `interactive` mode can be reproduced by running the next flow:

```
Your input ->  Are you a human?  (intent_live_chat)
I don't support live chat, but you can leave feedback for my team to review.  (utter_live_chat)
Would you like to submit any feedback?  (utter_live_chat_feedback)
(action_listen)
Your input ->  yes  (intent_core_yes)
Great. You can type in your feedback below and I'll share it with my team.  (utter_closing_type_below)
(form_closing_feedback)
(action_listen)
Your input ->  stuff  (intent_core_yes)
(form_closing_feedback)
I'll share that with my team to review.  (utter_closing_share_feedback)
My team is continually training me so that I'm able to expand the ways I can help you. Your interactions and feedback are key to my ability to grow and learn.  (utter_closing_finally)
Is there anything else I can help you with today?  (utter_closing_anything_else)
(action_listen)
Your input ->  yes  (intent_core_yes)
```

Here is where we get different predictions depending if running `rasa shell` or `rasa interactive`

### Rasa shell

Rasa shell doesn't output anything and runs an `action_listen`

```
(action_listen)
Your input ->
```

### Rasa interactive

Rasa interactive does the following:
```
Is there anything else I can help you with today? (utter_core_how_can_i_help)
(action_listen)
Your input ->
```

The difference is basically missing the story `Closing - finally - more help` with the action `utter_core_how_can_i_help`.

## Debug files

Debug files running on both `rasa shell` and `rasa interactive` under the [logs folder](./logs)

- [rasa interactive debug log](./logs/rasa-interactive-debug.log)
- [rasa shell debug log](./logs/rasa-shell-debug.log)

### Rasa test story

A test story that behaves like the interactive mode is also provided. It passes without failures or warnings.

- [tests/test_stories.yml](./tests/test_stories.yml)
