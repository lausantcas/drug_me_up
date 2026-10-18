## Project files

- public/index.html — original standalone front end, including the embedded FDA medication database. Netlify publishes *only public/*.
- netlify/functions/medbot.mjs — server-side Gemini call. The function is never served as a public HTML asset.
- netlify.toml — Netlify publish directory and function configuration.
- .env.example — names of optional settings ONLY; do not add your actual API key to this folder or a Git repository.

## Framework

Pill Patrol was built with the help of AI tools. *Claude* (Anthropic), *ChatGPT* (OpenAI) and *Gemini* (Google) were used to generate the code for public/index.html. They were also used to create the example dataset: 17 sample medications built into the app, each with a typical dosing interval, food and alcohol guidance, and a list of known interaction pairs.

*FDA medication data.* Besides the sample medications, the app includes real drug data from the US Food and Drug Administration. We downloaded files 1 and 2 (of 14) of the openFDA drug label data from the FDA download page: https://open.fda.gov/data/downloads/. From each drug label we kept only what the app uses (generic and brand names, active substances, dosage text and interaction text) and embedded it in public/index.html as additional medications. This adds 4,904 drug entries to the search, 1,291 of them with interaction text, and means the page does not need a connection to the FDA while it runs. Files 3 to 14 are not included, so some drugs are missing.

## One-time publication (recommended: Netlify + GitHub)

1. *Google: visit https://aistudio.google.com/api-keys . Create a **new, dedicated Gemini auth key* for Pill Patrol, not a key used by other apps. Set API restrictions to Gemini only if the key type requires this. Store the key privately.
2. *GitHub: make a new **private* repository (recommended) and upload the *contents* of this project folder, including .gitignore, netlify.toml, public/ and netlify/functions/. Do *not* upload real .env files or paste your key into source code.
3. *Netlify: go to https://app.netlify.com/ and choose **Add new project -> Import an existing project*. Connect GitHub, select the repository, let Netlify read netlify.toml and deploy. It publishes public/ and bundles the function automatically.
4. *Netlify secret: in your project open **Project configuration -> Environment variables -> Add a variable. Name: GEMINI_API_KEY, value: your dedicated Google Gemini key. Mark **Contains secret values* if offered; apply it to production and to Functions scope if your plan allows scope selection. *Never put it in netlify.toml.* Redeploy the site after adding/changing it.
5. Optional: Set GEMINI_ENABLED to true (default); GEMINI_MODEL to gemini-3.5-flash (default). No values are required for these options.
6. Test the temporary your-site.netlify.app address: log a sample medicine, open MedBot, ask a general question, and confirm you receive an AI response. The *MedBot privacy* option is OFF by default; enabling it sends name+dose for logged medications with each question.
7. *Domain: in Netlify **Domain management -> Add a domain you already own, enter pillpatrol.health. Netlify will tell you the exact records. In GoDaddy **My Products -> pillpatrol.health -> DNS* set the apex A/ALIAS and www CNAME as instructed by Netlify, removing only conflicting site/parking records, NOT MX/TXT used for email. Set primary domain and enable/verify HTTPS. You keep the domain at GoDaddy.



### Browser data when moving from file:// to https://pillpatrol.health

Browsers keep localStorage separately for each origin. Previous medication logs saved while opening HTML as a local file do *not* automatically appear on your new domain. This package does not add accounts or cloud storage. Preserve the older file/browser data until you have made a backup.

## How to pause, rotate or revoke the key

*Pause AI immediately* (subject to deployment/config propagation): In Netlify's environment variables set GEMINI_ENABLED=false, then redeploy. Or remove GEMINI_API_KEY and redeploy. The MedBot UI remains but displays the limited built-in fallback; the rest of Pill Patrol continues working. Existing in-flight calls may complete.

*Rotate: In https://aistudio.google.com/api-keys create a **new dedicated key, update the Netlify GEMINI_API_KEY secret value, redeploy, test the new key, and then revoke/disable the **old* key in Google Cloud Console / Google AI Studio. Google recommends verifying the replacement before deleting the old key.

*Revoke now: In https://console.cloud.google.com/apis/credentials select the Google Cloud project and **disable or delete the affected API key* (or use AI Studio's key management interface). This stops requests using that key even if it remains configured in Netlify. Remove it from Netlify afterwards and redeploy. Revoking the key does not take the website down, only Gemini answers.

*Limit impact: Netlify enforces **8 requests/60 seconds/IP+domain*, but this is not strong authentication and does not guarantee a spending cap. Restrict the key to Gemini, configure Google quotas / budget alerts (alerts are not hard spending limits), monitor usage, and consider CAPTCHA, user authentication or a server-wide quota before a high-traffic public launch.

## Privacy and clinical safety

- A user’s medication log remains in that user's browser. By default MedBot sends Google the *question only* plus short chat history, not the full medication log. Opting into *MedBot privacy -> Share logged medication names and doses* sends up to 25 names and doses with each question.
- Chat prompts can themselves include personal or sensitive medical information. Do not enter personally identifiable details; provide appropriate notice/privacy policy and consent before public use. Consult the Google Gemini API data use terms for your billing tier.
- Pill Patrol is an educational demo. Its built-in interval estimates, interaction text matching and generated advice must not be relied on to change, delay, or prescribe real doses.
- The key stays out of users' browsers, HTML files and browser requests. It still exists in Netlify's private secret store and Google systems, so no service can claim absolute security.

## Simple deployment check

The /api/medbot Netlify function *will not run when opening public/index.html as a local file:// page*. It requires deployment to Netlify with the environment secret set. Any fallback answer shown locally is from the original built-in MedBot notes, not Gemini.

## References

- Netlify functions: https://docs.netlify.com/build/functions/overview/
- Netlify secret variables: https://docs.netlify.com/build/environment-variables/secrets-controller/
- Netlify function rate-limiting: https://docs.netlify.com/manage/security/secure-access-to-sites/rate-limiting/
- Gemini API key security and rotation: https://ai.google.dev/gemini-api/docs/api-key


## More Information

This project was developed for the IKU Womxn In Stem Hackathon.
