# Hugo

Netlify usa la versione 0.19 di Hugo, quindi occorre fare le prove con questa versione specifica.


## Comandi

#### Build semplice (con 'watch')
> ./bin/hugo_0.19 --cleanDestinationDir --baseURL iMac.local --bind 192.168.178.59 --verbose --theme TRJ-theme --watch

#### 'server' (con 'watch')
> ./bin/hugo_0.19 server --cleanDestinationDir --baseURL iMac.local --bind 192.168.178.59 --verbose --theme TRJ-theme --watch


## Parametri extra per vedere 
--buildFuture --buildDrafts --buildExpired


