# My additional notes on flux

## Intro

I also added notifications to Discord from Flux.

![Notification](images/2021-11-15%2016_16_43-Flux-Discord.png)

## Steps

- In the "cluster/base/flux-system/notifications" folder there are the base settings for this. Modify these according to your needs.

- The file "cluster/base/discord-secret.sops.yaml" extends the "cluster/base/cluster-secrets.sops.yaml" file with the address of the Discor webhook.

- See [here](https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks) the creation of a webhook.
