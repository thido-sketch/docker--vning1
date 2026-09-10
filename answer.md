## Steg 1

1. Varför kopieras package*.json före resten av koden?
   - Detta görs för att optimera byggtiden genom att utnyttja Dockers inbyggda cache-system (layer caching).Genom att först kopiera package*.json och därefter köra installationen (npm ci), sparas installationen av beroendena i ett eget lager.  När du senare gör ändringar i din källkod (och kopierar in resten av koden efteråt) behöver Docker inte installera om alla paket varje gång, utan kan återanvända cachen, vilket gör att framtida byggen går mycket snabbare.

2. Vad händer med node_modules från steg 1 – var tar den vägen?
   - Dockerfilen använder en så kallad "multi-stage build".  I det första steget byggs appen i en Node-miljö.  I det andra steget skapas den slutgiltiga imagen ("bara det här blir imagen") utifrån en Nginx-image (nginx:1.27-alpine).  Eftersom koden endast kopierar den färdigbyggda mappen (COPY --from-build/app/dist...) till den nya Nginx-imagen, lämnas node_modules och all annan källkod kvar i det första steget. Den tar därmed ingen plats alls i din slutgiltiga, optimerade container.

3. Varför finns det ingen CMD?
   - Den slutgiltiga imagen baseras på nginx:1.27-alpine.  Officiella bas-images (som Nginx) har redan ett standardkommando för CMD inbakat i sig från skaparna (i det här fallet ett kommando som startar Nginx i förgrunden).Eftersom vi vill använda Nginx standardbeteende för att servera filerna behöver vi inte skriva över detta med ett eget CMD. Vår container ärver helt enkelt startkommandot från Nginx-imagen.


## Steg 2

- building tog 14.1 s 

## Steg 3

- building tog 3.9 s
- 87.1MB (disk usage) och 27.6MB (content size)