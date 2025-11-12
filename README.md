# Compte Rendu TP

---

### Contributeurs:

- FELTRIN Mathis
- PITAR Cyril

## Question 1)

> Rappeler le calcul réalisé pour chiffrer ce message en utilisant RSA. Rappeler comment l’opération inverse (déchiffrement) est réalisée.
> 

- Formule de chiffrement : $C \equiv M^e \pmod n$ .
- Formule de déchiffrement : $M \equiv C^d \pmod n$.
- $d$ est l’inverse modulaire de $e$ modulo $\varphi(n)$.

## Question 2)

> Rappeler le rôle de la méthode Diffie-Hellman.
> 

Le rôle de la méthode Diffie–Hellman est de **permettre à deux correspondants d’établir une clé secrète commune** sur un canal non sécurisé, **sans jamais échanger directement cette clé** .

Cette clé servira ensuite de **clé de session (souvent éphémère)** pour chiffrer les communications.

## Question 3)

> Donner 4 informations importantes contenues dans un certificat
> 

### 1. **L’identité du propriétaire**

- Nom de domaine ou organisation à laquelle le certificat est délivré.
- Exemples :
    - `CN = www.exemple.com` (Common Name)
    - `O = Exemple SARL`, `C = FR`

### 2. **La clé publique du propriétaire**

- C’est la **clé publique RSA, ECC, etc.** associée au propriétaire du certificat.

### 3. **Les informations sur l’autorité de certification (CA)**

- Le **nom de la CA** qui a signé le certificat.
- Sa **signature numérique** sur le certificat.

### 4. **La période de validité**

- Deux dates :
    - `Not Before` → début de validité,
    - `Not After` → expiration du certificat.
- Nom de domaine ou organisation à laquelle le certificat est délivré.
- Exemples :
    - `CN = www.feltrima.fr` (Common Name)
    - `O = Exemple SARL`, `C = FR`

## Question 4)

> Lorsque Bob se connecte en https au site [www.alice.com](http://www.alice.com/), indiquer, en les détaillant un
minimum, **toutes** les étapes pour l’authentification du site de Alice.
> 

## Question 5)

> Quelle est la longueur du « n » généré ?
> 
> 
> Rappeler les calculs mis en œuvre pour chiffrer un message M puis pour déchiffrer ?
> 
> Le « publicExponent » est-il difficile à deviner pour un pirate ? 
> Est-ce problématique ?
> 

**Réponse guidée :**

- Le **n** (modulus) fait **1024 bits**, c’est-à-dire la longueur de la clé RSA.
- Le **chiffrement** RSA se fait avec la formule :
    
    $C=M^e \mod  n$
    

- Le **déchiffrement** se fait avec :
    
    $M=C^d \mod  n$
    

- Le **public exponent e** (souvent 65537) est connu publiquement, donc **facile à deviner**, mais **ce n’est pas un problème**, car la sécurité repose sur la difficulté de factoriser `n` (trouver `p` et `q`).

## Question 6)

> Y a-t-il un intérêt à chiffrer une clé publique ?
> 
> 
> À chiffrer une clé privée ?
> 

- Chiffrer la **clé publique** ne sert à rien, car elle est faite pour être partagée librement.
- Chiffrer la **clé privée** est essentiel pour empêcher son vol ou son utilisation non autorisée.

## Question 7)

> Rechercher sur Internet quel est l’encodage utilisé pour la clé ?
> 
> 
> Quel est l’avantage de cet encodage ?
> 

- L’encodage utilisé est le **PEM** (*Privacy Enhanced Mail*).
- C’est une **représentation Base64** entourée de lignes `----BEGIN ...-----` et `----END ...-----`.
- **Avantage :**
    - lisible en texte (tu peux la copier dans un mail, sur GitHub, etc.),
    - très portable entre systèmes (Linux, Windows, macOS),
    - et compatible avec de nombreux outils (OpenSSL, serveurs web, etc.).

## Question 8)

> Retrouve-t-on bien les éléments attendus ?
> 
> 
> Pourquoi est-il intéressant de disposer d’un fichier à part contenant uniquement la clé publique ?
> 

- Oui ✅ : on retrouve bien le **modulus (n)** et le **public exponent (e)**.
- Il est intéressant d’avoir un fichier séparé car :
    - on peut **le diffuser librement** à d’autres utilisateurs,
    - cela **évite tout risque de fuite** de la clé privée,
    - et cela simplifie les échanges (serveurs, certificats, etc.).

## Question 9)

> Quelle clé doit-on utiliser pour chiffrer un message RSA lorsqu’on veut l’envoyer de manière confidentielle ?
> 

- On **chiffre avec la clé publique du destinataire** (ton voisin).
- Ainsi, **lui seul** pourra le déchiffrer avec **sa clé privée**.

## Question 10)

> Rechercher tous les paramètres de la commande openssl pkeyutl -encrypt … pour
chiffrer un message.
> 

```bash
openssl pkeyutl -encrypt -pubin -inkey pub.voisin.pem -in clair.txt -out cipher.bin
```

**Détail des options :**

- `encrypt` → indique qu’on chiffre,
- `pubin` → on utilise une clé publique en entrée,
- `inkey` → chemin vers la clé publique du voisin,
- `in` → message en clair,
- `out` → message chiffré.

## Question 11)

> Les contenus des différents messages chiffrés sont-ils identiques ? 
Est-ce normal ? Justifier.
> 

Les deux fichiers ne sont pas identiques.

```bash
openssl pkeyutl -decrypt -inkey rsa_keys_cyphered.pem -in cipher.bin -out dechiffre.txt
```

## Question 12)

> Expliquer le rôle de cette option. 
Après avoir parcouru le résultat de la commande s_client, indiquer le nombre de certificats qui ont été envoyés par le serveur.
> 

- **Rôle** : `showcerts` force l’affichage complet de tous les certificats renvoyés par le serveur (cert serveur + CA intermédiaires).
- **Nombre de certificats** : compte les blocs `----BEGIN CERTIFICATE----- ... -----END CERTIFICATE-----` qui apparaissent dans la sortie. (Copie/colle la sortie dans un fichier puis `grep -c "BEGIN CERTIFICATE"` pour compter.)

```bash
openssl s_client -showcerts -connect www.univ-grenoble-alpes.fr:443 </dev/null 2>/dev/null | \
  grep -c "BEGIN CERTIFICATE"
```

Grâce à cette commande, nous avons trouvé 3 certificats :

1. Certificat du serveur (*.univ-grenoble-alpes.fr)
2. Certificat CA racine (USERTrust RSA Certification Authority)
3. Certificat CA racine (USERTrust RSA Certification Authority)

## Question 13)

> Que signifie le x509 dans la commande saisie ? 
Quel est le sujet du certificat ? 
Expliquer complètement la notation utilisée dans le sujet (C=FR, ST=Auvergne…). 
Que signifie CN ? 
Quel est l’organisme qui a délivré le certificat ?
> 

- `x509` : format standard de certificat (norme ITU-T X.509).
- **Sujet (Subject)** : correspond au propriétaire du certificat, par ex `C=FR, ST=..., O=..., CN=*.univ-grenoble-alpes.fr`.
    - `C` = country (pays), `ST` = state / province, `L` = locality (ville), `O` = organization, `OU` = organizational unit, `CN` = Common Name (nom principal).
- **CN** (Common Name) : nom de domaine principal pour lequel le certificat est émis (ex. `.univ-grenoble-alpes.fr`).
- **Émetteur (Issuer)** : organisation qui a délivré/signé le certificat (affiché par `Issuer:` dans la sortie).

## Question 14)

> Que représente le « s », le « i » ?
> 

- `s:` = **subject** (le sujet du certificat — le propriétaire, ex. le serveur).
- `i:` = **issuer** (l’émetteur — l’autorité qui a signé ce certificat).

## Question 15)

> Quelle partie d'une clé RSA, le certificat contient-il ? 
Quels algorithmes ont été utilisés pour signer le certificat ? 
Que contient l’attribut CN présent dans le « sujet » ? 
Quel attribut contient les autres noms de machine pour lequel le certificat peut être utilisé ? Quelle est la durée de validité du certificat ? 
Quel est l'utilité du lien pointant vers un fichier .crl ?
> 
- **Quelle partie d'une clé RSA contient le certificat ?**
    
    → Il contient la **clé publique** du propriétaire : `Public Key` (par ex `Public-Key: (2048 bit)` et le `Modulus` / `Exponent`).
    
- **Algorithme de signature** : la ligne `Signature Algorithm` indique ce qui a été utilisé (ex. `sha256WithRSAEncryption` → RSA-SHA256).
- **Que contient l’attribut CN du sujet ?**
    
    → Le `CN` contient le **nom commun** (ex. `www.example.com` ou un wildcard `*.example.com`).
    
- **Quel attribut contient les autres noms de machine (SAN) ?**
    
    → **Subject Alternative Name (X509v3 Subject Alternative Name)** contient d’autres DNS, adresses IP, etc.
    
- **Durée de validité** : `Not Before` et `Not After` (dates indiquant la période pendant laquelle le certificat est valide).
- **Utilité du lien CRL** : la `CRL Distribution Points` est un emplacement (URL) où la liste de révocation (Certificate Revocation List) est disponible — sert à vérifier si le certificat a été révoqué avant sa date d’expiration.

## Question 16)

> Par qui le certificat de [www.univ-grenoble-alpes.fr](http://www.univ-grenoble-alpes.fr/) a-t-il été signé ? 
Soit E l’algorithme de chiffrement RSA, donner la formule utilisée pour calculer la signature présente dans le certificat de [www.univ-grenoble-alpes.fr](http://www.univ-grenoble-alpes.fr/) .
> 
- **Qui a signé ?** → l’`Issuer` du certificat → C=NL, O=GEANT Vereniging, CN=GEANT OV RSA CA 4
- **Formule mathématique (RSA)** : si l’algorithme de signature est RSA, la signature est calculée en signant le haché du `tbsCertificate` (to-be-signed certificate) avec la **clé privée** de l’issuer :
    
    $signature = (Hash(tbsCertificate))^{d_{CA}} \mod n_{CA}$
    
    où $d_{CA}$ et $n_{CA}$ sont la clé privée (exposant privé) et le modulus de la CA.
    

En pratique : CA calcule `Hash(tbsCertificate)` (ex SHA-256), puis chiffre ce hash avec sa clé privée (RSA) → résultat = signature.

## Question 17)

> Quelle est la taille de la clé publique présente dans ce certificat ? 
Par quelle CA ce certificat a-t-il été signé ?
> 

La taille de la clé publique est de 4096 bit.

CA : C=US, ST=New Jersey, L=Jersey City, O=The USERTRUST Network, CN=USERTrust RSA Certification Authority

## Question 18)

> Quel certificat permet de valider ce certificat ? 
Où se trouve-t-il ?
> 

```bash
# Commande pour vérifier le certificat
openssl verify -CAfile cert2.pem -untrusted cert1.pem cert0.pem
```

- Le certificat de dernier niveau (le plus haut avant la racine) est signé par la **racine**.

```bash
openssl verify -CAfile root-ca1.pem -untrusted cert1.pem cert0.pem

# >>> cert0.pem: OK
```

## Question 19)

> Comparer les champs subject et issuer. Qu’en déduisez-vous ? 
Donnez la formule qui a permis de générer la signature de ce certificat ? 
Comment s’appelle ce type de certificat ?
> 

Les champs **Subject** et **Issuer** sont identiques :

```mathematica
Subject: C = US, O = The USERTRUST Network, CN = USERTrust ECC Certification Authority
Issuer:  C = US, O = The USERTRUST Network, CN = USERTrust ECC Certification Authority
```

Cela signifie que le certificat s’est **signé lui-même**, il s’agit donc d’un **certificat racine auto-signé**.

La signature a été calculée selon la formule :

$$
Signature = \text{Chiffrer}_{\text{Clé privée du signataire}}(H(\text{Données du certificat}))
$$

soit, ici :

$$
Signature = \text{RSA}_{\text{Clé privée de USERTrust RSA}}(\text{SHA-384}(\text{TBS\_Certificate}))
$$

**Type de certificat :** certificat racine auto-signé (*self-signed root CA certificate*).

## Question 20)

> Quelle est le type de clé utilisée ? 
Quelle est la taille de la clé ? 
Quelle courbe est utilisée ? 
Quelle est la durée de validité ? 
Qu’est-ce qui vous permet de dire qu’il s’agit d’un certificat « racine » ou auto-signé ? 
Pour quels « X509v3 Key Usage » cette autorité de certification peut-elle être utilisée ?
> 

Le certificat de l’autorité de certification racine est défini dans le fichier de configuration `openssl.cnf` à la ligne :

```
certificate = $dir/certs/ca.cert.pem
```

soit le chemin complet :

`/home/camanager/ca/certs/ca.cert.pem`.

L’analyse du contenu du certificat avec la commande :

```bash
openssl x509 -in /home/camanager/ca/certs/ca.cert.pem -noout -text
```

### 🔹 **Type de clé utilisée**

Le champ `Public Key Algorithm` indique :

```
Public Key Algorithm: id-ecPublicKey
```

➡️ Cela signifie que la clé utilisée est une **clé elliptique (EC)**.

---

### 🔹 **Taille et courbe utilisée**

Les lignes suivantes précisent :

```
Public-Key: (256 bit)
ASN1 OID: prime256v1
NIST CURVE: P-256
```

➡️ La clé est donc de **256 bits**, basée sur la **courbe elliptique NIST P-256 (prime256v1)**.

---

### 🔹 **Algorithme de signature**

Le certificat est signé avec :

```
Signature Algorithm: ecdsa-with-SHA256
```

➡️ Il utilise l’algorithme **ECDSA avec SHA-256** pour la signature.

---

### 🔹 **Durée de validité**

Le bloc `Validity` indique :

```
Not Before: Nov  1 14:42:40 2025 GMT
Not After : Oct 27 14:42:40 2045 GMT
```

➡️ La durée de validité du certificat est de **20 ans**.

---

### 🔹 **Auto-signature / certificat racine**

Les champs `Subject` et `Issuer` sont identiques :

```
Subject: C=FR, ST=Savoie, L=Chambéry, O=TP Sécurité, CN=Root Lorne
Issuer:  C=FR, ST=Savoie, L=Chambéry, O=TP Sécurité, CN=Root Lorne
```

➡️ Cela prouve que le certificat a été **signé par lui-même**.

C’est donc un **certificat auto-signé**, également appelé **certificat racine**.

---

### 🔹 **Usages autorisés (X509v3 Key Usage)**

```
X509v3 Key Usage: critical
    Digital Signature, Certificate Sign, CRL Sign
```

➡️ Cette autorité de certification peut :

- **Signer d’autres certificats** (autorités intermédiaires ou serveurs)
- **Signer les listes de révocation (CRL)**
- **Effectuer des signatures numériques**

---

## Question 21)

> Quelle valeur avez-vous mise dans le paramètre dir de la section CA_default ? 
Dans quel dossier et sous quel nom la clé privée de la CA devra-t-elle être stockée ? 
Dans quel dossier et sous quel nom le certificat de la CA devra-t-il être stocké ?
> 

```bash
dir = /home/etudiant/ca
```

Selon la ligne suivante du fichier `openssl.cnf` :

```bash
private_key = $dir/private/intermediate.key.pem
```

➡️ La clé privée sera donc stockée dans :

```
/home/etudiant/ca/private/intermediate.key.pem
```

Selon cette ligne :

```bash
certificate = $dir/certs/intermediate.cert.pem
```

➡️ Le certificat sera stocké dans :

```
/home/etudiant/ca/certs/intermediate.cert.pem
```

## Question 22)

> Relever la commande saisie pour créer la clé.
> 

```bash
openssl genrsa -aes128 -passout pass:feltrima -out private/intermediate.key.pem 3072
```

## Question 23)

> Pourquoi est-ce que la présence de cette signature peut être qualifiée d’incongru ?
> 

Cette signature peut sembler **incongrue** car elle apparaît avant que le certificat ne soit signé par une autorité de certification.

Toutefois, elle est indispensable : elle garantit que la demande provient bien du détenteur de la clé privée et que son contenu n’a pas été altéré avant la signature officielle par la CA.

```bash
openssl x509 -in certs/feltrima.cert.pem -noout -text
```

## Question 24)

> Sur quelle machine est-il pertinent de générer la clé de chiffrement asymétrique qui
sera utilisée par le serveur ?
> 

La clé du serveur doit être générée **directement sur la machine tls-serv-feltrima** (le serveur web) puisque la **clé privée du serveur** doit rester **confidentielle** et **ne jamais quitter la machine** où elle sera utilisée.

Seule la **demande de signature (CSR)** sera envoyée à l’autorité de certification intermédiaire.

```bash
sudo openssl genrsa -out /etc/pki/tls/private/serveur_http.pem 2048
```

```bash
sudo openssl req -new -key /etc/pki/tls/private/serveur_http.pem \
-out serveur_http.csr.pem \
-subj "/C=FR/ST=Rhone/L=Lyon/O=Canut feltrima inc./CN=www.feltrima.fr" \
-addext "subjectAltName = DNS:www.feltrima.fr, DNS:dev.feltrima.fr"
```

```bash
openssl req -in serveur_http.csr.pem -noout -text
```

```bash
openssl ca -config openssl.cnf -extensions server_cert \
-days 375 -notext -md sha256 \
-in csr/serveur_http.csr.pem \
-out certs/serveur_http.cert.pem
```

```bash
sudo cp /etc/pki/tls/private/serveur_http.pem nginx-reverse/ssl/
cp /etc/pki/tls/certs/serveur_http.cert.pem nginx-reverse/ssl/
```

```bash
http://192.168.170.121/
# L'URL / et toutes les URL autres que /admin/ sont servies par web1.

http://192.168.170.121/picture/
# Picture web1

http://192.168.170.121/admin/
# L'URL /admin/ et les sous URL de /admin/ sont servies par web2.

http://192.168.170.121/admin/styles/
# Styles web2
```

## Question 25)

> Justifier que la 3ème solution est la plus pertinente.
> 

La troisième solution est la plus pertinente, car :

- Elle repose sur le **principe hiérarchique de la PKI** : une autorité racine (Root Lorne) signe les autorités intermédiaires, qui elles-mêmes signent les certificats serveurs.
- Faire confiance **uniquement à la racine** permet de **valider automatiquement toute la chaîne de certification**sans avoir à installer plusieurs certificats.
- C’est la **solution la plus simple à administrer et la plus sécurisée**, car la racine est unique et protégée, et sa confiance s’étend à tous les certificats qu’elle autorise.

```bash
sudo update-ca-trust extract
```

## Question 26)

> Quelle modification (ligne modifiée ou ajoutée) avez-vous effectuée dans le fichier hosts ?
> 

```bash
192.168.170.121   www.feltrima.fr
```

## Question 27)

> Rédiger un scénario de tests (objectif du test, résultat attendu) pour valider que l’authentification du serveur est menée à bien complètement. 
Mettre en œuvre les tests et relever les résultats obtenus. 
Vérifier également que la communication est chiffrée entre le client et le serveur (vous pouvez réaliser des captures de trames sur les machines en utilisant tcpdump ou tshark).
>