---
mainImage: ../../images/part-3.svg
part: 3
letter: c
---

<div class="content">

Ennen kun siirrymmen osan varsinaiseen aiheeseen, eli tiedon tallettamiseen tietokantaan, tarkastellaan muutamaa tapaa Node-sovellusten debuggaamiseen.

### Node-sovellusten debuggaaminen

Nodella tehtyjen sovellusten debuggaaminen on jossain määrin hankalampaa kuin selaimessa toimivan Javascriptin. Vanha hyvä keino on tietysti konsoliin tulostelu. Se kannattaa aina. On mielipiteitä, joiden mukaan konsoliin tulostelun sijaan olisi syytä suosia jotain kehittyneempää menetelmää, mutta en ole ollenkaan samaa mieltä. Jopa maailman aivan eliittiin kuuluvat open source -kehittäjät [käyttävät](https://tenderlovemaking.com/2016/02/05/i-am-a-puts-debuggerer.html) tätä [menetelmää](https://swizec.com/blog/javascript-debugging-slightly-beyond-console-log/swizec/6633).

#### Visual Studio Code

Visual Studio Coden debuggeri voi olla hyödyksi joissain tapauksissa. Seuraavassa screenshot, missä koodi on pysäytetty kesken uuden muistiinpanon lisäyksen

![](../images/3/17a.png)

Koodi on pysähtynyt nuolen osoittaman <i>breakpointin</i> kohdalle ja konsoliin on evaluoitu muuttujan <i>request.params</i> arvo. Vasemmalla olevassa ikkunassa on nähtävillä myös muuta ohjelman tilaan liittyvää.

Ylhäällä olevista nuolista yms. voidaan kontrolloida debuggauksen etenemistä.

Itse en juurikaan käytä Visual Studio Code debuggeria.

#### Chromen dev tools

Debuggaus onnisuu myös Chromen developer-konsolilla, käynnistämällä sovellus komennolla:

```bash
node --inspect index.js
```

Debuggeriin pääsee käsiksi kirjoittamalla chromen osoiteriville

```bash
chrome://inspect
```

Avautuvasta näkymästä valitaan debugattava sovellus:

![](../assets/3/18.png)

Debuggausnäkymä toimii kuten React-koodia debugattaessa, <i>Sources</i>-välilehdelle voidaan esim. asettaa breakpointeja, eli kohtia joihin suoritus pysähtyy:

![](../images/3/19a.png)

Kaikki sovelluksen console.log-tulostukset tulevat debuggerin <i>Console</i>-välilehdelle. Voit myös tutkia siellä muuttujien arvoja ja suorittaa mielivaltaista Javascript-koodia:

![](../images/3/20a.png)

#### Epäile kaikkea

Full Stack -sovellusten debuggaaminen vaikuttaa alussa erittäin hankalalta. Kun kohta kuvaan tulee myös tietokanta ja frontend on yhdistetty backendiin, on potentiaalisia virhelähteitä todella paljon.

Kun sovellus "ei toimi", onkin selvitettävä missä vika on. On erittäin yleistä, että vika on sellaisessa paikassa, mitä ei osaa ollenkaan epäillä, ja menee minuutti-, tunti- tai jopa päiväkausia ennen kuin oikea ongelmien lähde löytyy.

Avainasemassa onkin systemaattisuus. Koska virhe voi olla melkein missä vaan, kaikkea pitää epäillä, ja tulee pyrkiä poissulkemaan ne osat tarkastelusta, missä virhe ei ainakaan ole. Konsoliin kirjoitus, Postman, debuggeri ja kokemus auttavat.

Virheiden ilmaantuessa ylivoimaisesti huonoin strategia on jatkaa koodin kirjoittamista. Se on tae siitä, että koodissa on pian kymmenen ongelmaa lisää ja niiden syyn selvittäminen on entistäkin vaikeampaa. Toyota Production Systemin periaate [Stop and fix](http://gettingtolean.com/toyota-principle-5-build-culture-stopping-fix/#.Wjv9axP1WCQ) toimii tässäkin yhteydessä paremmin kuin hyvin.

## Mongo

Jotta saisimme talletettua muistiinpanot pysyvästi, tarvitsemme tietokannan. Useimmilla laitoksen kursseilla on käytetty relaatiotietokantoja. Tällä kurssilla käytämme [MongoDB](https://www.mongodb.com/):tä, joka on ns. [dokumenttitietokanta](https://en.wikipedia.org/wiki/Document-oriented_database).

Dokumenttitietokannat poikkeavat jossain määrin relaatiotietokannoista niin datan organisointitapansa kuin kyselykielensäkin suhteen. Dokumenttitietokantojen ajatellaan kuuluvan sateenvarjotermin [NoSQL](https://en.wikipedia.org/wiki/NoSQL) alle. Lisää dokumenttitietokannoista ja NoSQL:stä Tietokantojen perusteiden [viikon 7 materiaalista](https://materiaalit.github.io/tikape-s17/part7/).

**Lue nyt Tietokantojen perusteiden dokumenttitietokantoja kuvaava osuus.** Jatkossa oletetaan, että hallitset käsitteet _dokumentti_ ja _kokoelma_ (collection).

MongoDB:n voi luonnollisesti asentaa omalle koneelle. Internetistä löytyy kuitenkin myös palveluna toimivia Mongoja (esim [mlab](https://mlab.com/) ja [MongoDbCloud](https://www.mongodb.com/cloud/atlas)) ja seuraava ohje olettaa, että käytössä on jo vuosien kokemuksella luotettavaksi havaittu [mlab](https://mlab.com/).

Mlab-kanta on helppo ottaa käyttöön suoraan [Herokun kautta](https://elements.heroku.com/addons/mongolab); vaikka tämä on maksutonta, edellyttää se luottokorttitietojen antamista Herokulle.

Määrittelemmekin seuraavassa kannan suoraan [mlab](https://mlab.com/):iin, jolloin luottokorttitietoja ei tarvita.

Aloita luomalla mlabiin käyttäjätili, saatuasi mlabilta verifiointimailin ja kirjauduttuasi mailin linkin kautta sisään, voit luoda tietokannan:

![](../images/3/12a.png)

Määrittele esim. _Amazon web services_, tyypiksi ilmainen _sandbox_ ja sijoituspaikaksi Irlanti ja anna kannalle sopiva nimi.

Kun kanta on hetken kuluttua valmis, mene tietokannan hallintanäkymään

![](../images/3/12b.png)

Näkymä kertoo _MongoDB URI:n_ eli osoitteen, jonka avulla sovelluksemme käyttämä MongoDB-kirjasto saa yhteyden kantaan.

Osoite näyttää seuraavalta:

```
mongodb://<dbuser>:<dbpassword>@ds211088.mlab.com:11088/fullstack-notes
```

Tarvitsemme kannan käyttöä varten _dbuserin_ eli käyttäjätunnuksen. Käyttäjätunnuksen luominen tapahtuu tietokannan hallintanäkymästä

![](../images/3/12c.png)

Jos luotiin käyttäjätunnus _fullstack_ jonka salasana on _sekred_, on tietokannan osoite seuraava:

```
mongodb://fullstack:sekred@ds211088.mlab.com:11088/fullstack-notes
```

Olemme nyt valmiina kannan käyttöön.

Voisimme käyttää kantaa Javascript-koodista suoraan Mongon virallisen
[MongoDB Node.js driver](https://mongodb.github.io/node-mongodb-native/) -kirjaston avulla, mutta se on ikävän työlästä. Käytämmekin hieman korkeammalla tasolla toimivaa [mongoose](http://mongoosejs.com/index.html)-kirjastoa.

Mongoosesta voisi käyttää luonnehdintaa _object document mapper_ (ODM), ja sen avulla Javascript-olioiden tallettaminen mongon dokumenteiksi on suoraviivaista.

Asennetaan mongoose:

```bash
npm install mongoose --save
```

Ei lisätä mongoa käsittelevää koodia heti backendin koodin sekaan, vaan tehdään erillinen kokeilusovellus tiedostoon _mongo.js_:

```js
const mongoose = require('mongoose');

// korvaa url oman tietokantasi urlilla. ethän laita salasanaa Githubiin!
const url =
  'mongodb://fullstack:sekred@ds211088.mlab.com:11088/fullstack-notes';

mongoose.connect(url);

const Note = mongoose.model('Note', {
  content: String,
  date: Date,
  important: Boolean,
});

const note = new Note({
  content: 'HTML on helppoa',
  date: new Date(),
  important: true,
});

note.save().then(response => {
  console.log('note saved!');
  mongoose.connection.close();
});
```

Kun koodi suoritetaan komennolla _node mongo.js_ lisää mongoose tietokantaaan uuden dokumentin.

Mlab:in hallintanäkymä näyttää lisäämämme datan:

![](../images/3/13a.png)

Kuten näkymä kertoo, on muistiinpanoa vastaava _dokumentti_ lisätty kokoelmaan (collection) nimeltään _notes_.

Koodi sisältää muutamia mielenkiintoisia asioita. Aluksi avataan yhteys ja määritellään, että mongoose käyttää _promiseja_, eikä oldschool-takaisinkutsufunktioita:

```js
const mongoose = require('mongoose');

const url =
  'mongodb://fullstack:fullstack@ds211088.mlab.com:11088/fullstack-notes';

mongoose.connect(url);
```

Valitettavasti mongoosen dokumentaatiossa käytetään joka paikassa takaisinkutsufunktioita, joten sieltä ei kannata suoraan copypasteta koodia, sillä promisejen ja vanhanaikaisten callbackien sotkeminen samaan koodiin ei ole kovin järkevää.

<div class='important'>
Kannattaa muistaa, että tietokannan salasanaa ei kannata laittaa missään vaiheessa Githubiin!
</div>

### Skeema

Yhteyden avaamisen jälkeen määritellään mustiinpanoa vastaava [model](http://mongoosejs.com/docs/models.html):

```js
const Note = mongoose.model('Note', {
  content: String,
  date: Date,
  important: Boolean,
});
```

Modelin parametrina määritellään _muistiinpanon_ [skeema](http://mongoosejs.com/docs/guide.html), joka kertoo mongooselle, miten muistiinpano-oliot tulee tallettaa tietokantaan.

Ensimmäisenä parametrina oleva _Note_ määrittelee, että mongoose tallettaa muistiinpanoa vastaavat oliot kokoelmaan nimeltään _notes_ sillä [mongoosen konventiona](http://mongoosejs.com/docs/models.html) on määritellä kokoelmien nimet monikossa (esim. _notes_), kun niihin viitataan modelin määrittelyssä yksikkömuodossa (esim. _Note_).

Mongoosen dokumentaatiossa skeema ja sitä vastaava model määritellään kumpikin erikseen:

```js
const noteSchema = new mongoose.Schema({
  content: String,
  date: Date,
  important: Boolean,
});

const Note = mongoose.model('Note', noteSchema);
```

Koska meillä ei ole skeema-oliolle muuta käyttöä kuin modelin parametrina, käytämme hyväksemme sitä, että skeema voidaan määritellä modeleille suoraan antamalla toisena parametrina skeeman määrittelevä olio.

Dokumenttikannat, kuten Mongo ovat _skeemattomia_, eli tietokanta itsessään ei välitä mitään sinne talletettavan tiedon muodosta. Samaan kokoelmaankin on mahdollista tallettaa olioita joilla on täysin eri kentät.

Mongoosea käytettäessä periaatteena on kuitenkin se, että tietokantaan talletettavalle tiedolle määritellään _sovelluksen koodin tasolla skeema_, joka määrittelee minkä muotoisia olioita kannan eri kokoelmiin talletetaan.

### Olioiden luominen ja tallettaminen

Seuraavaksi sovellus luo muistiinpanoa vastaavan [model](http://mongoosejs.com/docs/models.html):in avulla muistiinpano-olion:

```js
const note = new Note({
  content: 'Selain pystyy suorittamaan vain javascriptiä',
  date: new Date(),
  important: false,
});
```

Modelit ovat ns. _konstruktorifunktioita_, jotka luovat parametrien perusteella Javascript-olioita. Koska oliot on luotu modelien konstruktorifunktiolla, niillä on kaikki modelien ominaisuudet, eli joukko metodeja, joiden avulla olioita voidaan mm. tallettaa tietokantaan.

Tallettaminen tapahtuu metodilla _save_. Metodi palauttaa _promisen_, jolle voidaan rekisteröidä _then_-metodin avulla tapahtumankäsittelijä:

```js
note.save().then(result => {
  console.log('note saved!');
  mongoose.connection.close();
});
```

Kun olio on tallennettu kantaan, kutsutaan _then_:in parametrina olevaa tapahtumankäsittelijää, joka sulkee tietokantayhteyden komennolla <code>mongoose.connection.close()</code>. Ilman yhteyden sulkemista ohjelman suoritus ei pääty.

Tallennusoperaation tulos on takaisinkutsun parametrissa _result_. Yhtä olioa tallentaessamme tulos ei ole kovin mielenkiintoinen, olion sisällön voi esim. tulostaa konsoliin jos haluaa tutkia sitä tarkemmin sovelluslogiikassa tai esim. debugatessa.

Talletetaan kantaan myös pari muuta muistiinpanoa muokkaamalla dataa koodista ja suorittamalla ohjelma uudelleen.

### Olioiden hakeminen tietokannasta

Kommentoidaan koodista uusia muistiinpanoja generoiva osa, ja korvataan se seuraavalla:

```js
Note.find({}).then(result => {
  result.forEach(note => {
    console.log(note);
  });
  mongoose.connection.close();
});
```

Kun koodi suoritetaan, kantaan talletetut muistiinpanot tulostuvat.

Oliot haetaan kannasta _Note_-modelin metodilla [find](http://mongoosejs.com/docs/api.html#find_find). Metodin parametrina on hakuehto. Koska hakuehtona on tyhjä olio <code>{}</code>, saimme kannasta kaikki _notes_-kokoelmaan talletetut oliot.

Hakuehdot noudattavat mongon [syntaksia](https://docs.mongodb.com/manual/reference/operator/).

Voisimme hakea esim. ainoastaan tärkeät muistiinpanot seuraavasti:

```js
Note.find({ important: true }).then(result => {
  // ...
});
```

</div>

<div class="tasks">

### Tehtäviä

Tee nyt tehtävä [3.12](/tehtävät#mongoosen-alkeet)

</div>

<div class="content">

## Tietokantaa käyttävä backend

Nyt meillä on periaatteessa hallussamme riittävä tietämys ottaa mongo käyttöön sovelluksessamme.

Aloitetaan nopean kaavan mukaan, copypastetaan tiedostoon _index.js_ mongoosen määrittelyt, eli

```js
const mongoose = require('mongoose');

const url =
  'mongodb://fullstack:sekred@ds211088.mlab.com:11088/fullstack-notes';

mongoose.connect(url);

const Note = mongoose.model('Note', {
  content: String,
  date: Date,
  important: Boolean,
});
```

ja muutetaan kaikkien muistiinpanojen hakemisesta vastaava käsittelijä seuraavaan muotoon

```js
app.get('/api/notes', (request, response) => {
  Note.find({}).then(notes => {
    response.json(notes);
  });
});
```

Voimme todeta selaimella, että backend toimii kaikkien dokumenttien näyttämisen osalta:

![](../assets/3/14.png)

Toiminnallisuus on muuten kunnossa, mutta frontend olettaa, että olioiden yksikäsitteinen tunniste on kentässä _id_. Emme myöskään halua näyttää frontendille mongon versiointiin käyttämää kenttää <em>\_\_v</em>. Tehdään pieni apufunktio, jonka avulla yksittäinen muistiinpano saadaan muutettua mongon sisäisestä esitysmuodosta haluamaamme muotoon:

```js
const formatNote = note => {
  return {
    content: note.content,
    date: note.date,
    important: note.important,
    id: note._id,
  };
};
```

ja palautetaan HTTP-pyynnön vastauksena funktion avulla muotoiltuja oliota:

```js
app.get('/api/notes', (request, response) => {
  Note.find({}).then(notes => {
    response.json(notes.map(formatNote));
  });
});
```

Nyt siis muuttujassa _notes_ on taulukollinen mongon palauttamia olioita. Kun suoritamme operaation <code>notes.map(formatNote)</code> seurauksena on uusi taulukko, missä on jokaista alkuperäisen taulukon alkiota vastaava funktion _formatNote_ avulla muodostettu alkio.

Jos kannasta haettavilla olioilla olisi suuri määrä kenttiä, apufunktio _formatNote_ kannattaisi muotoilla hieman geneerisemmässä muodossa, esim:

```js
const formatNote = note => {
  const formattedNote = { ...note._doc, id: note._id };
  delete formattedNote._id;
  delete formattedNote.__v;

  return formattedNote;
};
```

Ensimmäinen rivi luo uuden olion, mihin kopioituu kaikki vanhan olion kentät. Uuteen olioon lisätään myös kenttä _id_:

```js
const formattedNote = { ...note._doc, id: note._id };
```

Ennen olion palauttamista turhat kentät poistetaan.

Jos ohjelma käyttäisi muunkin tyyppisiä olioita kuin _muistiinpanoja_ sopisi sama funktio niidenkin muotoiluun. Jatkon kannalta on kuitenkin parempi, että pidämme alkuperäisen version funktiosta.

On myös mahdollista estää mongoosea palauttamasta tiettyjen kenttien arvoa, tai pyytää sitä palauttamaan vain tietyt kentät. Saamme estettyä parametrin <em>\_\_v</em>:n lisäämällä _find_-metodiin toiseksi parametriksi _{\_\_v: 0}_ seuraavasti:

```js
app.get('/api/notes', (request, response) => {
  Note.find({}, { __v: 0 }).then(notes => {
    response.json(notes.map(formatNote));
  });
});
```

Kyselyjen palauttamien kenttien määrittely tapahtuu Mongon [syntaksin mukaan](https://docs.mongodb.com/manual/tutorial/project-fields-from-query-results/).

### Tietokantamäärittelyjen eriyttäminen omaksi moduuliksi

Ennen kuin täydennämme backendin muutkin osat käyttämään tietokantaa, eriytetään mongoose-spesifinen koodi omaan moduuliin.

Tehdään moduulia varten hakemisto _models_ ja sinne tiedosto _note.js_:

```js
const mongoose = require('mongoose');

const url =
  'mongodb://fullstack:sekred@ds211088.mlab.com:11088/fullstack-notes';

mongoose.connect(url);

const Note = mongoose.model('Note', {
  content: String,
  date: Date,
  important: Boolean,
});

module.exports = Note;
```

Noden [moduulien](https://nodejs.org/docs/latest-v8.x/api/modules.html) määrittely poikkeaa hiukan osassa 2 määrittelemistämme frontendin käyttämistä [ES6-moduuleista](/osa2/#refaktorointia---moduulit).

Moduulin ulos näkyvä osa määritellään asettamalla arvo muuttujalle _module.exports_. Asetamme arvoksi määritellyn modelin _Note_. Muut moduulin sisällä määritellyt asiat, esim. muuttujat _mongoose_ ja _url_ eivät näy moduulin käyttäjälle.

Moduulin käyttöönotto tapahtuu lisäämällä tiedostoon _index.js_ seuraava rivi

```js
const Note = require('./models/note');
```

Näin muuttuja _Note_ saa arvokseen saman olion, jonka moduuli määrittelee.

### Muut operaatiot

Muutetaan nyt kaikki operaatiot tietokantaa käyttävään muotoon.

Uuden muistiinpanon luominen tapahtuu seuraavasti:

```js
app.post('/api/notes', (request, response) => {
  const body = request.body;

  if (body.content === undefined) {
    return response.status(400).json({ error: 'content missing' });
  }

  const note = new Note({
    content: body.content,
    important: body.important || false,
    date: new Date(),
  });

  note.save().then(savedNote => {
    response.json(formatNote(savedNote));
  });
});
```

Muistiinpano-oliot siis luodaan _Note_-konstruktorifunktiolla. Pyyntöön vastataan _save_-operaation takaisinkutsufunktion sisällä. Näin varmistutaan, että operaatio vastaus tapahtuu vain jos operaatio on onnistunut. Palaamme virheiden käsittelyyn myöhemmin.

Takaisinkutsufunktion parametrina oleva _savedNote_ on talletettu muistiinpano. HTTP-pyyntöön palautetaan kuitenkin siitä funktiolla _formatNote_ formatoitu muoto:

```js
response.json(formatNote(savedNote));
```

Yksittäisen muistiinpanon tarkastelu muuttuu muotoon

```js
app.get('/api/notes/:id', (request, response) => {
  Note.findById(request.params.id).then(note => {
    response.json(formatNote(note));
  });
});
```

### Frontendin ja backendin yhteistominnallisuuden varmistaminen

Kun backendia laajennetaan, kannattaa sitä testailla aluksi **ehdottomasti selaimella, postmanilla tai VS Coden REST clientillä:**. Seuraavassa kokeillaan uuden muistiinpanon luomista tietokannan käyttöönoton jälkeen:

![](../images/3/14b.png)

Vasta kun kaikki on todettu toimivaksi, kannattaa siirtyä testailemaan että muutosten jälkeinen backend toimii yhdessä myös frontendin kanssa. Kaikkien kokeilujen tekeminen ainoastaan frontendin kautta on todennäköisesti varsin tehotonta.

Todennäköisesti voi olla kannattavaa edetä frontin ja backin integroinnissa toiminnallisuus kerrallaan, eli ensin voidaan toteuttaa esim. kaikkien muistiinpanojen näyttäminen backendiin ja testata että toiminnallisuus toimii selaimella. Tämän jälkeen varmistetaan, että frontend toimii yhteen muutetun backendin kanssa. Kun kaikki on todettu olevan kunnossa, siirrytään seuraavan ominaisuuden toteuttamiseen.

Kun kuvioissa on mukana tietokanta, on tietokannan tilan tarkastelu mlabin hallintanäkymästä varsin hyödyllistä, usein myös suoraan tietokantaa käyttävät Node-apuohjelmat, kuten tiedostoon _mongo.js_ kirjoittamamme koodi auttavat sovelluskehityksen edetessä.

Sovelluksen tämän hetkinen koodi on kokonaisuudessaan [githubissa](https://github.com/FullStack-HY/part3-notes-backend/tree/part3-3), tagissa _part3-3_.

</div>

<div class="tasks">

### Tehtäviä

Tee nyt tehtävät [3.13-3.15](/tehtävät#backend-ja-tietokanta)

</div>

<div class="content">

### Virheiden käsittely

Jos yritämme mennä selaimella sellaisen yksittäisen muistiinpanon sivulle, jota ei ole olemassa, eli esim. urliin <http://localhost:3001/api/notes/5a3b80015b6ec6f1bdf68d> missä _5a3b80015b6ec6f1bdf68d_ ei ole minkään tietokannassa olevan muistiinpanon tunniste, jää selain "jumiin" sillä palvelin ei vastaa pyyntöön koskaan.

Palvelimen konsolissa näkyykin virheilmoitus:

![](../assets/3/15.png)

Kysely on epäonnistunut ja kyselyä vastaava promise mennyt tilaan _rejected_. Koska emme käsittele promisen epäonnistumista, ei pyyntöön vastata koskaan. Osassa 2 tutustuimme jo [promisejen virhetilanteiden käsittelyyn](/osa2#promise-ja-virheet).

Lisätään tilanteeseen yksinkertainen virheidenkäsittelijä:

```js
app.get('/api/notes/:id', (request, response) => {
  Note.findById(request.params.id)
    .then(note => {
      response.json(formatNote(note));
    })
    .catch(error => {
      console.log(error);
      response.status(404).end();
    });
});
```

Kaikissa virheeseen päättyvissä tilanteissa HTTP-pyyntöön vastataan statuskoodilla 404 not found. Konsoliin tulostetaan tarkempi tieto virheestä.

Tapauksessamme on itseasiassa olemassa kaksi erityyppistä virhetilannetta. Toinen vastaa sitä, että yritetään hakea muistiinpanoa virheellisen muotoisella _id_:llä, eli sellasiella mikä ei vastaa mongon id:iden muotoa.

Jos teemme näin tulostuu konsoliin:

<pre>
Method: GET
Path:   /api/notes/5a3b7c3c31d61cb9f8a0343
Body:   {}
---
{ CastError: Cast to ObjectId failed for value "5a3b7c3c31d61cb9f8a0343" at path "_id"
    at CastError (/Users/mluukkai/opetus/_fullstack/osa3-muisiinpanot/node_modules/mongoose/lib/error/cast.js:27:11)
    at ObjectId.cast (/Users/mluukkai/opetus/_fullstack/osa3-muisiinpanot/node_modules/mongoose/lib/schema/objectid.js:158:13)
    ...
</pre>

Toinen virhetilanne taas vastaa tilannetta, missä haettavan muistiinpanon id on periaatteessa oikeassa formaatissa, mutta tietokannasta ei löydy indeksillä mitään:

<pre>
Method: GET
Path:   /api/notes/5a3b7c3c31d61cbd9f8a0343
Body:   {}
---
TypeError: Cannot read property '_doc' of null
    at formatNote (/Users/mluukkai/opetus/_fullstack/osa3-muisiinpanot/index.js:46:33)
    at Note.findById.then.note (/Users/mluukkai/opetus/_fullstack/osa3-muisiinpanot/index.js:65:21)
</pre>

Nämä tilanteet on syytä erottaa toisistaan, ja itseasiassa jälkimmäinen poikkeus on oman koodimme <code>/Users/mluukkai/opetus/\_fullstack/osa3-muisiinpanot/index.js:46</code> aiheuttama.

Muutetaan koodia seuraavasti:

```js
app.get('/api/notes/:id', (request, response) => {
  Note.findById(request.params.id)
    .then(note => {
      if (note) {
        response.json(formatNote(note));
      } else {
        response.status(404).end();
      }
    })
    .catch(error => {
      console.log(error);
      response.status(400).send({ error: 'malformatted id' });
    });
});
```

Jos kannasta ei löydy haettua olioa, muuttujan _note_ arvo on _undefined_ ja koodi ajautuu _else_-haaraan. Siellä vastataan kyselyyn _404 not found_.

Jos id ei ole hyväksyttävässä muodossa, ajaudutaan _catch_:in avulla määriteltyyn virheidenkäsittelijään. Sopiva statuskoodi on [400 bad request](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.1) koska kyse on juuri siitä:

> The request could not be understood by the server due to malformed syntax. The client SHOULD NOT repeat the request without modifications.

Vastaukseen on lisätty myös hieman dataa kertomaan virheen syystä.

Promisejen yhteydessä kannattaa melkeinpä aina lisätä koodiin myös virhetilainteiden käsittely, muuten seurauksena on usein hämmentäviä vikoja.

Ei ole koskaan huono idea tulostaa poikkeuksen aiheuttanutta olioa konsoliin virheenkäsittelijässä:

```js
.catch(error => {
  console.log(error)
  response.status(400).send({ error: 'malformatted id' })
})
```

Virheenkäsittelijään joutumisen syy voi olla joku ihan muu kuin mitä on tullut alunperin ajatelleeksi. Jos virheen tulostaa konsoliin, voi säästyä pitkiltä ja turhauttavilta väärää asiaa debuggaavilta sessioilta.

Aina kun ohjelmoit ja projektissa on mukana backend **tulee ehdottomasti koko ajan pitää silmällä backendin konsolin tulostuksia**. Jos työskentelet pienellä näytöllä, riittää että konsolista on näkyvissä edes pieni kaistale:

![](../images/3/15b.png)

### loput operaatiot

Toteutetaan vielä jäljellä olevat operaatiot, eli yksittäisen muistiinpanon poisto ja muokkaus.

Poisto onnistuu helpoiten metodilla [findByIdAndRemove](http://mongoosejs.com/docs/api.html#findbyidandremove_findByIdAndRemove):

```js
app.delete('/api/notes/:id', (request, response) => {
  Note.findByIdAndRemove(request.params.id)
    .then(result => {
      response.status(204).end();
    })
    .catch(error => {
      response.status(400).send({ error: 'malformatted id' });
    });
});
```

Vastauksena on statauskoodi _204 no content_ molemmissa "onnistuneissa" tapauksissa, eli jos olio poistettiin tai olioa ei ollut mutta _id_ oli periaatteessa oikea. Takaisinkutsun parametrin _result_ perusteella olisi mahdollisuus haarautua ja palauttaa tilanteissa eri statuskoodi jos sille on tarvetta.

Muistiinpanon tärkeyden muuttamisen mahdollistava olemassaolevan muistiinpanon päivitys onnistuu helposti metodilla [findOneAndUpdate](http://mongoosejs.com/docs/api.html#findoneandupdate_findOneAndUpdate). Tässä ja myöhemmin sivulla on _findOneAndUpdate_, mutta koodissa alla [findByIdAndUpdate](http://mongoosejs.com/docs/api.html#findbyidandupdate_findByIdAndUpdate), joka vastaa `findOneAndUpdate({ _id: id }, ...)` kutsua.

```js
app.put('/api/notes/:id', (request, response) => {
  const body = request.body;

  const note = {
    content: body.content,
    important: body.important,
  };

  Note.findByIdAndUpdate(request.params.id, note, { new: true })
    .then(updatedNote => {
      response.json(formatNote(updatedNote));
    })
    .catch(error => {
      console.log(error);
      response.status(400).send({ error: 'malformatted id' });
    });
});
```

Operaatio mahdollistaa myös muistiinpanon sisällön editoinnin. Päivämäärän muuttaminen ei ole mahdollista.

Huomaa, että metodin _findOneAndUpdate_ parametrina tulee antaa normaali Javascript-olio, eikä uuden olion luomisessa käytettävä _Note_-konstruktorifunktiolla luotu olio.

Pieni, mutta tärkeä detalji liittyen operaatioon _findOneAndUpdate_. Oletusarvoisesti tapahtumankäsittelijä saa parametrikseen _updatedNote_ päivitetyn olion [ennen muutosta](http://mongoosejs.com/docs/api.html#findoneandupdate_findOneAndUpdate) olleen tilan. Lisäsimme operaatioon parametrin <code>{ new: true }</code> jotta saamme muuttuneen olion palautetuksi kutsujalle.

Backend vaikuttaa toimivan postmanista ja VS Code REST clientistä tehtyjen kokeilujen perusteella ja myös frontend toimii moitteettomasti tietokantaa käyttävän backendin kanssa.

</div>

<div class="tasks">

### Tehtäviä

Tee nyt tehtävät [3.16-3.18](/tehtävät#lisää-operaatioita)

</div>
