Le Royaume Active Directory - De Novice à Gardien des Clés
Introduction : Bienvenue dans le Royaume Numérique
Ce rapport vous invite à un voyage. Nous n'allons pas simplement étudier un logiciel ; nous allons explorer un royaume numérique. Active Directory (AD) est ce royaume. Vous, futur administrateur, en êtes le gardien. Les utilisateurs sont les citoyens, les ordinateurs sont les habitations et les forteresses, les données sont les trésors et les secrets d'État. Les attaquants sont les espions, les assassins et les armées ennemies qui cherchent à s'emparer des "clés du royaume".
Active Directory n'est pas un simple annuaire ; c'est la pierre angulaire de l'infrastructure informatique pour 90 % des entreprises mondiales. Il centralise la gestion de l'identité et l'authentification des utilisateurs et des ressources. Si Active Directory ne fonctionne pas, l'entreprise s'arrête. Comprendre sa structure est important, mais maîtriser sa sécurité est une responsabilité absolue. Ce guide est conçu pour vous faire passer de simple visiteur à gardien expert de ce royaume vital, en décomposant sa complexité en briques compréhensibles et en mettant un accent constant sur l'art de sa défense.
Partie I : Cartographier le Royaume - Les Structures Fondamentales d'Active Directory
La manière dont un royaume est organisé, avec ses provinces, ses cités et ses frontières, détermine sa capacité à se défendre et à prospérer. Il en va de même pour Active Directory. La manière dont vous structurez votre environnement en domaines, arbres et forêts n'est pas une simple question de rangement. C'est la première et l'une des plus fondamentales décisions de sécurité que vous prendrez. Une forêt est la frontière de sécurité ultime. Un domaine est une frontière administrative et de réplication. Une conception mal avisée de cette structure peut introduire des failles de sécurité systémiques avant même la création du premier utilisateur. Chaque choix architectural a des implications profondes sur la manière dont les permissions sont gérées et dont les attaques peuvent être contenues ou, au contraire, se propager.
Chapitre 1 : Le Domaine - La Capitale du Royaume
Le Service d'Annuaire : La Grande Bibliothèque du Royaume
Au cœur de tout royaume se trouve un registre central de ses habitants, de ses terres et de ses lois. Active Directory est, avant tout, un service d'annuaire. Imaginez une bibliothèque gigantesque qui ne contient pas de livres, mais des fiches détaillées sur chaque "objet" du royaume : chaque citoyen (compte utilisateur), chaque chariot (compte ordinateur), chaque guilde (groupe), et même chaque imprimante. Cet annuaire centralisé permet aux administrateurs de gérer, d'organiser et de sécuriser toutes les ressources du réseau à partir d'un seul endroit, éliminant le chaos de la gestion individuelle de chaque machine.
Le Domaine : La Cité-État
Un domaine est un groupe logique d'objets (utilisateurs, ordinateurs, etc.) qui partagent la même base de données d'annuaire et sont soumis à un ensemble commun de règles. C'est la capitale de votre territoire, une cité-état administrative. Par exemple, tous les citoyens et ressources de Ventes.MonRoyaume.com appartiennent à ce domaine. Il définit une frontière administrative claire : les lois (stratégies) et les administrateurs de ce domaine gèrent tout ce qui s'y trouve, et leurs pouvoirs s'arrêtent aux frontières du domaine.
Le Contrôleur de Domaine (DC) : Le Château Fort
Si le domaine est la capitale, le Contrôleur de Domaine (DC) est son château fort. Le DC est un serveur Windows qui héberge et protège la "grande bibliothèque" (la base de données Active Directory, ntds.dit). C'est le cœur battant du domaine, et son rôle est double et critique :
Authentification : Il agit comme le garde à l'entrée du château. Quand un utilisateur tente de se connecter à son poste de travail, c'est le DC qui vérifie son identité en comparant le nom d'utilisateur et le mot de passe fournis avec les informations stockées dans sa base de données.
Autorisation : Une fois l'utilisateur authentifié, le DC informe les autres services du réseau de ce que cet utilisateur a le droit de faire, en se basant sur ses permissions et ses appartenances à des groupes.
Pour garantir la résilience du royaume et éviter qu'il ne s'effondre si un château est assiégé (si un DC tombe en panne), il est crucial d'avoir au moins deux Contrôleurs de Domaine par domaine. Ces DC se répliquent constamment les informations entre eux grâce à un mécanisme de réplication multi-maître, où une modification sur un DC est automatiquement propagée aux autres.
sequenceDiagram
    participant Utilisateur
    participant Poste_de_Travail
    participant Controleur_de_Domaine as DC

    Utilisateur->>Poste_de_Travail: Saisie du login/mot de passe
    Poste_de_Travail->>DC: Demande d'authentification pour l'Utilisateur
    DC-->>DC: Vérification des identifiants dans la base AD
    alt Identifiants corrects
        DC-->>Poste_de_Travail: Authentification réussie (Ticket Kerberos)
        Poste_de_Travail-->>Utilisateur: Ouverture de la session
    else Identifiants incorrects
        DC-->>Poste_de_Travail: Échec de l'authentification
        Poste_de_Travail-->>Utilisateur: Message d'erreur
    end

Chapitre 2 : L'Arbre et la Forêt - Alliances et Empires
L'Arbre : Une Dynastie de Domaines
Un royaume peut s'étendre et donner naissance à de nouvelles provinces. Dans Active Directory, un arbre est une collection d'un ou plusieurs domaines qui partagent un espace de nom DNS contigu et hiérarchique.
Métaphore : Si MonRoyaume.com est le domaine parent (le roi fondateur), alors Ventes.MonRoyaume.com et RH.MonRoyaume.com sont des domaines enfants. Ils sont issus de la même lignée et forment ensemble une seule dynastie, un seul "arbre". Une relation de confiance bidirectionnelle et transitive est automatiquement établie entre le parent et l'enfant, facilitant le partage de ressources. Cette structure est souvent utilisée pour refléter l'organisation géographique ou départementale d'une grande entreprise.
La Forêt : La Frontière Ultime de l'Empire
La forêt est le conteneur de plus haut niveau dans la structure logique d'Active Directory. C'est un ensemble d'un ou plusieurs arbres qui ne partagent pas nécessairement le même espace de nom.
Métaphore : Notre empire (MonRoyaume.com) peut former une alliance stratégique avec un autre royaume acquis par mariage ou conquête (RoyaumeAcquis.net). Les deux arbres (MonRoyaume.com et RoyaumeAcquis.net) coexistent alors dans la même "forêt".
Importance Critique de la Forêt : La forêt est la frontière de sécurité. C'est le périmètre le plus large au sein duquel les accès peuvent être gérés. Tous les domaines d'une même forêt partagent deux éléments fondamentaux :
Un Schéma Commun : Le plan de construction qui définit ce qu'est un "objet" (quels attributs un utilisateur peut avoir, par exemple) est identique pour toute la forêt.
Un Catalogue Global (Global Catalog) : Un index partiel contenant les informations les plus importantes sur chaque objet de la forêt, permettant des recherches rapides à travers tous les domaines.
Une relation de confiance bidirectionnelle et transitive est automatiquement établie entre tous les domaines de la forêt. Cela signifie qu'un compte du domaine Ventes.MonRoyaume.com peut potentiellement accéder à une ressource dans Prod.RoyaumeAcquis.net si les permissions appropriées sont accordées. La décision de créer une ou plusieurs forêts est donc une décision de sécurité majeure qui définit le niveau d'isolation entre les différentes entités d'une organisation.
graph TD
    subgraph Forêt
        subgraph Arbre1
            A(MonRoyaume.com - Racine) --> B(Ventes.MonRoyaume.com)
            A --> C(RH.MonRoyaume.com)
        end
        subgraph Arbre2
            D(RoyaumeAcquis.net - Racine) --> E(Prod.RoyaumeAcquis.net)
        end
        A <-->|Relation de confiance<br>transitive| D
    end

Partie II : Les Habitants et les Lois du Royaume - Objets et Politiques
Une fois la carte du royaume établie, il est temps de s'intéresser à ses habitants et aux lois qui les régissent. Dans Active Directory, la manière dont les objets sont créés, groupés et gérés, ainsi que la façon dont les politiques (GPO) sont appliquées, définit directement la surface d'attaque. Une structure d'Unités d'Organisation (OU) désorganisée, un abus des groupes de sécurité, ou une mauvaise application des GPO ne sont pas de simples problèmes de "propreté" administrative ; ce sont des vulnérabilités actives. La délégation de contrôle, si elle n'est pas effectuée avec une précision chirurgicale, peut créer des chemins d'escalade de privilèges involontaires. La gestion des objets et des politiques est donc le prélude indispensable à une sécurité robuste.
Chapitre 3 : Les Habitants du Royaume - Objets AD
L'annuaire Active Directory est peuplé d'objets, qui sont les entités représentant les ressources du réseau. Ces objets se divisent en deux grandes catégories.
Objets Feuilles (Leaf Objects) : Ce sont les objets finaux, comme les citoyens et leurs biens. Ils ne peuvent pas contenir d'autres objets. Les exemples les plus courants sont les comptes d'Utilisateurs, les comptes d'Ordinateurs et les objets Imprimantes.
Objets Conteneurs (Container Objects) : Ce sont des objets qui peuvent en contenir d'autres, servant à organiser la structure. Les exemples incluent les Domaines, les Unités d'Organisation (OU) et les Groupes.
L'Unité d'Organisation (OU) : Les Ministères et Provinces
L'Unité d'Organisation (OU) est le conteneur le plus important et le plus flexible pour l'administration quotidienne.
Métaphore : Pensez aux OU comme aux ministères (Ventes, Finance, R&D) ou aux provinces (Paris, Lyon) de votre royaume. Elles vous permettent de regrouper des objets (principalement des utilisateurs et des ordinateurs) de manière logique pour refléter la structure de votre organisation.
Rôle Fondamental : La puissance des OU réside dans leur double fonction. Contrairement aux conteneurs par défaut comme "Users" ou "Computers", vous POUVEZ et DEVEZ utiliser les OU pour deux tâches critiques :
Lier des Stratégies de Groupe (GPO) : Pour appliquer des configurations spécifiques à un ensemble d'utilisateurs ou d'ordinateurs.
Déléguer des droits administratifs : Pour donner des permissions de gestion limitées à des équipes spécifiques.
Les Groupes : Les Guildes et Confréries
Les groupes sont des collections d'objets (utilisateurs, ordinateurs, ou même d'autres groupes) principalement utilisés pour gérer les permissions et les communications de manière efficace. Il est crucial de comprendre la différence entre les deux types principaux.
Groupes de Sécurité :
Métaphore : Ce sont les détenteurs de clés du royaume. Au lieu de donner une clé individuelle pour la salle du trésor à chaque membre de la garde royale, vous donnez une seule clé au "Groupe de la Garde Royale". Toute personne qui rejoint ce groupe peut utiliser cette clé.
Fonction : Leur but principal est d'assigner des permissions d'accès à des ressources partagées (fichiers, dossiers, imprimantes). Pour ce faire, ils possèdent un identifiant de sécurité unique (SID) et peuvent être ajoutés aux Listes de Contrôle d'Accès (ACL) des ressources. Ils peuvent également être utilisés comme liste de distribution d'e-mails.
Groupes de Distribution :
Métaphore : Ce sont des listes de diffusion pour les messagers du roi, permettant d'envoyer un décret à tous les nobles d'un coup.
Fonction : Leur seul et unique but est de créer des listes d'adresses e-mail (par exemple, touslesemployes@monroyaume.com) pour faciliter la communication. Ils ne sont pas "activés pour la sécurité", ce qui signifie qu'ils n'ont pas de SID et ne peuvent PAS être utilisés pour accorder des permissions.
Le choix entre ces deux types de groupes est une décision de gestion fondamentale. L'utilisation de groupes de sécurité est la norme pour la gestion des accès, tandis que les groupes de distribution sont réservés à la communication.
| Caractéristique | Groupe de Sécurité | Groupe de Distribution |
|---|---|---|
| Objectif Principal | Gérer les permissions et les accès aux ressources | Créer des listes d'e-mails pour la communication |
| Peut recevoir des permissions? | Oui (possède un SID, peut être dans une ACL) | Non (n'est pas un principal de sécurité) |
| Peut être utilisé pour envoyer des e-mails? | Oui | Oui |
| Cas d'usage typique | Donner l'accès au dossier "Comptabilité" | Envoyer une newsletter à toute l'entreprise |
| Considération de performance | Un trop grand nombre d'appartenances peut ralentir la connexion de l'utilisateur | Aucun impact sur la performance de connexion |
Chapitre 4 : La Délégation de Contrôle - Nommer des Gouverneurs Locaux
Il est à la fois dangereux et inefficace que seuls les "Admins du Domaine" (les conseillers les plus proches du roi) puissent tout faire dans le royaume. La délégation de contrôle est le mécanisme qui permet de confier des tâches administratives spécifiques sur des périmètres limités (généralement des OU) à d'autres utilisateurs ou groupes.
Métaphore : Le roi nomme un gouverneur pour la province de "Lyon". Ce gouverneur (par exemple, le groupe "Support Technique Lyon") reçoit le droit de gérer les citoyens (réinitialiser les mots de passe des utilisateurs) et les habitations (ajouter des ordinateurs au domaine) de sa province uniquement. Il n'a aucun pouvoir sur la province de "Paris" ou sur les affaires du royaume.
Comment ça marche? L'outil principal pour cela est l'Assistant de Délégation de Contrôle, accessible par un clic droit sur une OU. Un administrateur peut y accorder des permissions granulaires prédéfinies, comme "Réinitialiser les mots de passe des comptes d'utilisateur" ou "Gérer l'appartenance des groupes", à un utilisateur ou à un groupe spécifique.
Focus Sécurité : La délégation est un outil puissant pour appliquer le principe du moindre privilège, mais elle doit être maniée avec une extrême prudence. Une délégation mal configurée (par exemple, donner des droits sur une OU parente qui se propagent par héritage à des OU critiques contenant des serveurs ou des administrateurs) peut créer des failles de sécurité béantes. Il est impératif de ne jamais déléguer de contrôle sur les conteneurs par défaut (comme "Users") ou sur l'OU des contrôleurs de domaine.
graph TD
    A(Admin du Domaine) --"Délègue la tâche via l'Assistant"--> B(OU 'Ventes')
    subgraph B
        C[Utilisateur 'Jean Vente']
        D[Ordinateur 'PC-Jean']
    end
    E(Groupe 'Support Ventes') --"Reçoit la permission sur OU 'Ventes'"--> B
    F(Membre du 'Support Ventes') --"Peut maintenant réinitialiser le mot de passe de"--> C
    F -.->|N'a aucun droit sur| G(OU 'Finance')

Chapitre 5 : Les Stratégies de Groupe (GPO) - Le Code de Lois du Royaume
Les Objets de Stratégie de Groupe (GPO) sont le principal mécanisme pour définir, déployer et imposer des règles et des configurations de manière centralisée sur un grand nombre d'utilisateurs et d'ordinateurs.
Métaphore : Les GPO sont le code de lois du royaume. Une loi peut dire "Tous les citoyens du royaume doivent avoir un mot de passe d'au moins 10 caractères" (une GPO liée au domaine) ou "Dans la province des 'Ventes', le fond d'écran des ordinateurs doit être le logo de l'entreprise" (une GPO liée à l'OU 'Ventes').
Ce qu'on peut faire avec les GPO : La portée des GPO est immense. Elles permettent de déployer des logiciels (via des paquets MSI), de mapper des lecteurs réseau et des imprimantes, de configurer des centaines de paramètres de sécurité (règles de pare-feu, restrictions sur les ports USB, politique d'audit), de définir des fonds d'écran, de restreindre l'accès à des parties du système d'exploitation comme l'invite de commande, et bien plus encore.
L'Ordre d'Application (LSDOU) : C'est un concept fondamental pour comprendre et dépanner les GPO. Les politiques s'appliquent dans un ordre de précédence strict, chaque niveau pouvant écraser le précédent :
Local : D'abord, la politique de la machine locale elle-même.
Site : Ensuite, la politique liée au site Active Directory (un regroupement d'objets basé sur la topologie réseau physique).
Domaine : Puis, la ou les politiques liées au domaine.
OU (Unité d'Organisation) : Enfin, les politiques liées aux Unités d'Organisation, en commençant par l'OU la plus haute dans la hiérarchie et en descendant jusqu'à l'OU qui contient l'objet.
La Règle d'Or : La politique la plus proche de l'objet l'emporte. Si une GPO au niveau du domaine désactive l'invite de commande, mais qu'une GPO sur l'OU de l'utilisateur l'active, l'utilisateur AURA accès à l'invite de commande. Cette règle garantit une gestion granulaire.
Bonnes Pratiques de Sécurité pour les GPO :
Ne modifiez JAMAIS les politiques par défaut! La "Default Domain Policy" et la "Default Domain Controllers Policy" sont des objets critiques. La première ne doit être utilisée que pour définir la politique de mot de passe, la politique de verrouillage de compte et la politique Kerberos à l'échelle du domaine. La seconde ne doit servir qu'à définir les droits d'assignation des utilisateurs et la politique d'audit sur les contrôleurs de domaine. Pour toute autre configuration, créez de nouvelles GPO.
Structurez vos OU de manière logique : Une bonne structure d'OU est indispensable pour une gestion efficace des GPO. Séparez les utilisateurs et les ordinateurs dans des arborescences d'OU distinctes pour appliquer des politiques ciblées.
Liez les GPO au plus haut niveau possible : Pour éviter la duplication et simplifier la gestion, appliquez les GPO communes à la racine d'une arborescence d'OU (par exemple, une GPO de sécurité de base pour tous les ordinateurs) et laissez l'héritage faire le reste.
Partie III : La Garde Royale - Authentification et Contrôle d'Accès
Un royaume sécurisé repose sur deux piliers : savoir qui entre (l'authentification) et contrôler ce qu'ils peuvent faire une fois à l'intérieur (l'autorisation). Ces deux concepts sont distincts mais intrinsèquement liés. Le protocole Kerberos est le garde qui vérifie l'identité à la porte du royaume, prouvant qui vous êtes. Les Listes de Contrôle d'Accès (ACL) sont les lois internes qui décident de ce que vous pouvez faire une fois votre identité prouvée. Une faille dans Kerberos, comme un attaquant qui forge un laissez-passer universel (un Golden Ticket), rend toutes les lois internes inutiles, car l'attaquant peut se faire passer pour n'importe qui, y compris le roi. Inversement, des lois internes laxistes (des ACL mal configurées) peuvent donner des pouvoirs excessifs à un citoyen légitimement authentifié. La sécurité du royaume repose sur la robustesse de ces deux maillons de la chaîne.
Chapitre 6 : Kerberos - Le Protocole des Sceaux et des Laissez-passer
Kerberos est le protocole d'authentification par défaut dans un environnement de domaine Active Directory. Il a été conçu pour être robuste sur des réseaux considérés comme non-sûrs (comme Internet ou un réseau d'entreprise où un attaquant pourrait écouter le trafic), en évitant de transmettre les mots de passe en clair.
Métaphore du Protocole Royal :
Imaginez un processus bureaucratique royal pour obtenir des autorisations.
Les Acteurs :
Le Client (vous) : Le citoyen qui souhaite accéder à un service.
Le Service (par exemple, un serveur de fichiers) : La ressource que vous voulez utiliser (les archives royales).
Le KDC (Key Distribution Center) : Le bureau central des laissez-passer, une entité de confiance située sur le Contrôleur de Domaine (DC). Ce bureau a deux guichets :
Authentication Service (AS) : Le premier guichet, qui vérifie votre identité fondamentale.
Ticket Granting Service (TGS) : Le second guichet, qui délivre des laissez-passer pour des services spécifiques, sur présentation d'une preuve d'identité validée par le premier guichet.
Le Flux d'Authentification en 3 Étapes :
AS-REQ / AS-REP (Obtenir un Laissez-passer Général) :
Le client (vous) se présente au guichet AS du KDC et envoie une requête (AS-REQ) disant "Bonjour, je suis Bob". Pour prouver son identité sans crier son mot de passe, le client envoie un timestamp chiffré avec une clé dérivée de son mot de passe secret.
L'AS, qui connaît aussi le secret de Bob, tente de déchiffrer le timestamp. S'il y parvient, l'identité est prouvée. En réponse (AS-REP), l'AS renvoie deux choses : une clé de session pour communiquer avec le TGS, et un Ticket-Granting Ticket (TGT). Ce TGT est un laissez-passer général, valable pour une durée limitée (par défaut 10 heures), qui prouve que vous êtes bien Bob. Ce TGT est lui-même chiffré avec un secret que seul le KDC connaît : le hash du mot de passe du compte de service krbtgt. Le client ne peut donc pas lire ni modifier son propre TGT.
TGS-REQ / TGS-REP (Échanger le Laissez-passer Général contre un Laissez-passer Spécifique) :
Vous voulez maintenant accéder aux archives royales (le serveur de fichiers). Vous allez au guichet TGS du KDC.
Vous envoyez une requête (TGS-REQ) qui contient : votre TGT (prouvant que vous êtes Bob), un authentificateur (un nouveau timestamp chiffré avec la clé de session obtenue à l'étape 1), et le nom du service que vous visez (son Service Principal Name, ou SPN).
Le TGS déchiffre le TGT avec le secret krbtgt, vérifie sa validité, puis déchiffre l'authentificateur. Si tout est correct, il génère un Ticket de Service (ST), également appelé TGS dans certaines documentations. Ce ticket est un laissez-passer spécifique, valable uniquement pour le service "Archives Royales". Il est chiffré avec le secret du service "Archives Royales" et vous est renvoyé (TGS-REP).
AP-REQ / AP-REP (Présenter le Laissez-passer Spécifique au Service) :
Vous allez enfin aux "Archives Royales" et présentez votre Ticket de Service dans une requête d'application (AP-REQ).
Le garde des archives (le serveur de fichiers) utilise son propre secret (le hash de son mot de passe de compte machine ou de service) pour déchiffrer le ticket. À l'intérieur, il trouve votre identité (Bob) et d'autres informations de sécurité (comme les groupes auxquels vous appartenez, via une structure appelée PAC - Privilege Attribute Certificate). S'il parvient à déchiffrer le ticket, il sait qu'il est authentique et a été émis par le KDC. Il vous laisse alors entrer.
sequenceDiagram
    participant Client
    participant KDC_AS as KDC (Guichet AS)
    participant KDC_TGS as KDC (Guichet TGS)
    participant Service

    Client->>KDC_AS: 1. AS-REQ: Je suis Bob, je veux un TGT. (Preuve chiffrée avec mon secret)
    KDC_AS-->>Client: 2. AS-REP: Voici ton TGT (chiffré avec secret KDC) et une clé de session.

    Client->>KDC_TGS: 3. TGS-REQ: Voici mon TGT, je veux un ticket pour le Service X.
    KDC_TGS-->>Client: 4. TGS-REP: Voici ton Ticket de Service pour X (chiffré avec secret de X).

    Client->>Service: 5. AP-REQ: Voici mon Ticket de Service pour toi.
    Service-->>Client: 6. AP-REP (optionnel): Accès accordé/refusé.

Chapitre 7 : Les Listes de Contrôle d'Accès (ACL) - Qui a la Clé de Quelle Porte?
Une fois qu'un utilisateur est authentifié par Kerberos, ce sont les Listes de Contrôle d'Accès (ACL) qui déterminent ce qu'il peut réellement faire. Une ACL est une liste de permissions attachée à chaque objet sécurisable dans Active Directory et sur le système de fichiers (un fichier, un dossier, un utilisateur, une OU, une imprimante...).
Métaphore : Chaque porte, chaque coffre et chaque document dans le royaume a une liste de règles accrochée dessus. Cette liste indique précisément qui a le droit de l'ouvrir, et ce qu'il a le droit de faire une fois à l'intérieur (lire, écrire, détruire).
Les ACE (Access Control Entries) : L'ACL est composée d'une ou plusieurs Entrées de Contrôle d'Accès (ACE). Chaque ACE est une ligne sur cette liste de règles qui spécifie trois choses :
Principal de Sécurité : Pour qui cette règle s'applique. C'est l'identifiant (SID) d'un utilisateur, d'un groupe de sécurité ou d'un ordinateur.
Permission : Quel type d'accès est concerné (par exemple, Lire, Écrire, Contrôle total, Réinitialiser le mot de passe).
Type : S'agit-il d'une autorisation (Allow) ou d'une interdiction (Deny).
La Règle du "Deny" : Une règle de refus (Deny) explicite l'emporte toujours sur une règle d'autorisation (Allow). Si un utilisateur est membre de deux groupes, l'un autorisé et l'autre refusé, l'accès lui sera refusé. C'est une mesure de sécurité puissante mais qui doit être utilisée avec parcimonie car elle peut complexifier le dépannage.
Gestion via les Groupes de Sécurité : Il est fondamental de ne jamais assigner de permissions directement à des utilisateurs individuels. C'est une mauvaise pratique qui devient rapidement ingérable et impossible à auditer. La bonne pratique consiste à suivre le modèle AGDLP (Accounts go into Global groups, which go into Domain Local groups, which get Permissions) ou une variante. En termes simples : créez un groupe de sécurité pour un rôle ou un accès à une ressource, assignez les permissions à ce groupe, puis ajoutez les comptes utilisateurs nécessaires à ce groupe.
Partie IV : L'Art de la Guerre - Sécuriser le Royaume Contre les Menaces
Comprendre la structure et les lois du royaume est une chose ; le défendre en est une autre. La compromission d'Active Directory n'est pas un événement singulier, mais une chaîne d'attaque méthodique. Les attaquants ne visent que rarement le contrôleur de domaine dès le départ. Ils suivent un schéma prévisible : une compromission initiale d'un compte faible (souvent par phishing), une phase de reconnaissance pour cartographier le réseau, un mouvement latéral pour se déplacer de machine en machine, une escalade de privilèges pour obtenir des droits plus élevés, et enfin, la prise de contrôle des "clés du royaume" (le DC, le compte krbtgt) pour établir une persistance durable. La défense doit donc être pensée comme la rupture de cette chaîne à plusieurs endroits, et non comme la protection d'un seul point fortifié.
Chapitre 8 : La Mentalité du Défenseur - Connaître son Ennemi
Pour défendre le royaume, il faut penser comme un assaillant. Il faut comprendre où se trouvent les faiblesses et quels sont les objectifs de l'ennemi.
Une Surface d'Attaque Immense : La surface d'attaque d'Active Directory est partout. Chaque compte utilisateur, chaque ordinateur, chaque GPO mal configurée, chaque compte de service avec des permissions excessives est une porte potentielle pour un attaquant. Les services réseau exposés par un contrôleur de domaine (DNS sur le port 53, Kerberos sur le port 88, LDAP sur le port 389, SMB sur le port 445, RPC sur le port 135) sont autant de vecteurs d'attaque possibles.
Les Objectifs de l'Attaquant :
Vol d'Identifiants (Credential Theft) : L'objectif premier est d'obtenir des informations d'identification valides, que ce soit des mots de passe en clair ou, plus souvent, leurs "hashes" (empreintes cryptographiques).
Escalade de Privilèges (Privilege Escalation) : Une fois à l'intérieur avec un compte de base, l'attaquant cherche à transformer cet accès limité en un accès administrateur, lui donnant plus de pouvoir.
Mouvement Latéral (Lateral Movement) : Utiliser un compte compromis pour se déplacer d'un ordinateur à un autre au sein du réseau, se rapprochant progressivement des cibles de grande valeur.
Persistance : L'objectif ultime est d'établir un accès durable et discret au réseau, souvent en créant des portes dérobées ou en forgeant des tickets d'authentification à longue durée de vie, comme un Golden Ticket.
Chapitre 9 : Les Attaques Courantes Décortiquées
Pour contrer ces tactiques, il faut les comprendre en détail.
Pass-the-Hash (PtH)
Métaphore : Au lieu de voler la clé physique d'une porte, l'attaquant vole une empreinte parfaite de la clé. Le système d'authentification (NTLM, un protocole plus ancien mais souvent encore actif pour des raisons de compatibilité) ne vérifie pas la clé elle-même, mais son empreinte (le hash). L'attaquant n'a donc pas besoin de la clé, il lui suffit de "passer le hash" pour s'authentifier.
Comment ça marche : Un attaquant compromet une machine (par exemple, via un email de phishing). Il utilise ensuite un outil comme Mimikatz pour extraire de la mémoire vive du système les hashes NTLM des utilisateurs qui se sont connectés à cette machine. Armé de ces hashes, il peut alors se connecter à d'autres serveurs ou postes de travail du réseau en se faisant passer pour ces utilisateurs, sans jamais avoir besoin de connaître ou de craquer leurs mots de passe en clair.
Kerberoasting
Métaphore : Certains serviteurs du royaume (les comptes de service utilisés par les applications) ont des clés pour des portes de service importantes. Malheureusement, ces serviteurs ont souvent des mots de passe très simples et qui ne changent jamais ("service_imprimante123"). Un espion (l'attaquant), même avec une accréditation de base, peut légitimement demander au bureau des laissez-passer une copie du cadenas de cette porte de service. Le cadenas lui-même est solide, mais il est fermé avec la clé faible du serviteur. L'espion emporte le cadenas chez lui et essaie des millions de clés faibles (attaque par dictionnaire/force brute) jusqu'à ce qu'il l'ouvre. Il connaît maintenant la clé du serviteur et peut accéder à tout ce que cette clé protège.
Comment ça marche : N'importe quel utilisateur authentifié du domaine peut demander un Ticket de Service (ST/TGS) pour un service qui a un Service Principal Name (SPN) enregistré dans AD. Ce ticket, nécessaire pour accéder au service, est partiellement chiffré avec le hash du mot de passe du compte de service. L'attaquant demande légitimement ce ticket au KDC, le récupère, puis l'emporte "hors ligne" pour tenter de le craquer. Comme les mots de passe des comptes de service sont souvent faibles, cette attaque a un taux de réussite élevé.
Golden & Silver Tickets : Les Sceaux Royaux Falsifiés
Ce sont des attaques de post-exploitation qui supposent que l'attaquant a déjà obtenu des secrets critiques.
Golden Ticket :
Métaphore : C'est la falsification du sceau royal suprême, le grand sceau du royaume. Ce sceau est fabriqué à partir du secret le plus gardé : le hash du mot de passe du compte krbtgt. Si un attaquant vole ce secret, il peut créer un TGT (le laissez-passer général) pour n'importe qui (même un utilisateur qui n'existe pas), lui accorder n'importe quels privilèges (Admin du Domaine), et lui donner une durée de validité de plusieurs années. Il devient le roi de l'ombre, capable d'accéder à tout, partout, sans être détecté.
Impact : Accès total et persistant à l'ensemble du domaine. C'est le "game over" pour la sécurité de la forêt AD.
Silver Ticket :
Métaphore : C'est la falsification du sceau d'un gouverneur local (un service spécifique). L'attaquant ne vole pas le sceau royal, mais celui du service des archives (le serveur de fichiers). Il vole le hash du mot de passe de ce compte de service et l'utilise pour forger un Ticket de Service (ST) valide pour accéder à ce service uniquement.
Impact : L'accès est limité à un service spécifique, mais l'attaque est beaucoup plus discrète. Elle ne nécessite aucune communication avec le KDC une fois le ticket forgé, ce qui la rend très difficile à détecter. Si le service compromis est critique (une base de données RH, un serveur de fichiers financiers), les dégâts peuvent être immenses.
| Caractéristique | Golden Ticket Attack | Silver Ticket Attack |
|---|---|---|
| Ticket Falsifié | TGT (Ticket-Granting Ticket) | TGS/ST (Ticket-Granting Service) |
| Secret Requis | Hash NTLM du compte KRBTGT | Hash NTLM du compte de service cible |
| Portée de l'Accès | Toute la forêt/domaine (illimité) | Uniquement le service spécifique sur un serveur |
| Interaction avec le DC | L'attaquant utilise le TGT forgé pour demander des TGS légitimes au DC. | Aucune interaction avec le DC une fois le ticket forgé. L'authentification se fait directement auprès du service. |
| Persistance | Très élevée (tant que le mot de passe KRBTGT n'est pas changé 2 fois) | Élevée (tant que le mot de passe du service n'est pas changé) |
| Difficulté de Détection | Difficile, mais génère du trafic vers le DC (demandes de TGS) | Très difficile, car il n'y a pas de trafic anormal vers le DC. La détection doit se faire sur le serveur de service. |
Chapitre 10 : La Stratégie de Défense en Profondeur
Une forteresse imprenable ne repose pas sur un seul mur, mais sur de multiples couches de défense. La sécurisation d'Active Directory suit ce même principe de "défense en profondeur".
Le Principe du Moindre Privilège (PoLP) : La Règle d'Or
C'est le concept le plus fondamental de la cybersécurité : aucun utilisateur, service ou système ne doit avoir plus de droits que ce qui est strictement nécessaire pour accomplir sa tâche. Dans Active Directory, cela se traduit par des actions concrètes :
Auditer et réduire drastiquement le nombre de membres des groupes à hauts privilèges (Admins du Domaine, Admins de l'Entreprise, Admins du Schéma) au strict minimum.
Utiliser des comptes séparés pour les tâches administratives et les tâches quotidiennes (email, navigation web). Un administrateur ne devrait jamais consulter ses emails avec son compte à privilèges.
Ne jamais utiliser un compte à privilèges pour se connecter à une machine moins sécurisée. Un administrateur de domaine ne doit pas se connecter à un poste de travail standard.
Le Modèle de Tiers (Tier Model) : Cloisonner pour Mieux Régner
Recommandé par Microsoft et des agences comme l'ANSSI, ce modèle administratif divise l'infrastructure en niveaux (Tiers) pour contenir les attaques et empêcher les mouvements latéraux et l'escalade de privilèges.
Métaphore : Le royaume est divisé en cercles concentriques de confiance, comme une citadelle.
Tier 0 : Le Cœur du Royaume. Contient les actifs les plus critiques, les "clés du royaume". La compromission du Tier 0 signifie la compromission de tout le royaume. Il inclut : les Contrôleurs de Domaine, les comptes à hauts privilèges (Admins du Domaine, etc.), le compte krbtgt, et les systèmes qui gèrent ces actifs (comme les serveurs de PKI).
Tier 1 : Les Infrastructures Clés. Contient les serveurs d'entreprise et les applications critiques (serveurs de fichiers, bases de données, serveurs d'applications) et les comptes qui les administrent.
Tier 2 : Les Postes de Travail. Contient les ordinateurs des utilisateurs finaux et les comptes utilisateurs standards.
La Règle Fondamentale : Un niveau inférieur ne peut JAMAIS contrôler ou administrer un niveau supérieur. Un compte ou une machine du Tier 2 ne doit jamais avoir de contrôle sur une ressource du Tier 1 ou 0. Un administrateur de serveur (Tier 1) ne doit jamais pouvoir se connecter à un contrôleur de domaine (Tier 0). Ce cloisonnement strict empêche un attaquant qui a compromis un poste de travail (Tier 2) de se déplacer facilement pour atteindre un DC (Tier 0).
graph TD
    subgraph Tier 0 - Contrôle
        A
        B
    end
    subgraph Tier 1 - Gestion
        C
        D
    end
    subgraph Tier 2 - Utilisateurs
        E
        F[Comptes Utilisateurs]
    end

    D -- "Administre" --> C
    F -- "Utilise" --> E

    F -- "Ne peut PAS administrer" --x C
    D -- "Ne peut PAS administrer" --x A

    style A fill:#ff9999,stroke:#333,stroke-width:2px
    style B fill:#ff9999,stroke:#333,stroke-width:2px
    style C fill:#ffcc99,stroke:#333,stroke-width:2px
    style D fill:#ffcc99,stroke:#333,stroke-width:2px
    style E fill:#99ccff,stroke:#333,stroke-width:2px
    style F fill:#99ccff,stroke:#333,stroke-width:2px

Sécuriser les Contrôleurs de Domaine : Protéger le Château Fort
Les DC sont la cible ultime. Leur protection est non négociable.
Sécurité Physique : Les DC doivent être physiquement sécurisés dans des salles de serveurs verrouillées, avec un accès contrôlé.
Sécurité Logique :
Aucun accès à Internet ne doit être autorisé depuis un DC. La navigation web sur un DC est une faute professionnelle grave.
Appliquer les derniers correctifs de sécurité de manière rigoureuse et rapide.
Utiliser des pare-feu basés sur l'hôte pour restreindre les communications aux seuls ports et protocoles strictement nécessaires.
Interdire la connexion RDP aux DC, sauf depuis des stations de travail d'administration dédiées et sécurisées (PAW - Privileged Access Workstation).
Désactiver les services non essentiels comme le spouleur d'impression (Print Spooler), qui a été la source de multiples vulnérabilités d'escalade de privilèges.
Chapitre 11 : L'Arsenal du Défenseur - Bonnes Pratiques et Outils
La défense est un travail actif qui nécessite des outils et des processus robustes.
Audit et Surveillance Continus : Vous ne pouvez pas protéger ce que vous ne voyez pas.
Activez les stratégies d'audit avancées de Windows pour journaliser les événements de sécurité critiques : modifications des groupes à privilèges, échecs de connexion, création de comptes, accès aux objets, etc..
Utilisez des outils d'audit et de détection. PingCastle est un excellent outil pour réaliser un audit rapide de la maturité de sécurité de votre AD. BloodHound utilise la théorie des graphes pour visualiser les chemins d'attaque et les relations de privilèges complexes. Des solutions comme Microsoft Defender for Identity sont conçues pour détecter les comportements anormaux et les attaques en temps réel.
Gestion des Comptes à Privilèges (PAM - Privileged Access Management) : Au lieu de donner des droits administratifs permanents, mettez en place des solutions pour un accès "juste-à-temps" (JIT). Un administrateur demande des privilèges pour une durée limitée pour effectuer une tâche spécifique, puis ses droits sont automatiquement révoqués.
Gestion des Comptes de Service :
Chaque fois que possible, utilisez des comptes de service gérés par le groupe (gMSA). Leur mot de passe, long et complexe, est géré et changé automatiquement par Active Directory, ce qui neutralise les attaques par Kerberoasting.
Pour les comptes de service manuels, utilisez des mots de passe extrêmement longs (plus de 25 caractères) et complexes, et mettez en place un processus pour les faire tourner régulièrement.
Plan de Reprise Après Sinistre (Disaster Recovery) :
Avoir des sauvegardes régulières, testées et, surtout, hors ligne (air-gapped) de vos contrôleurs de domaine est non-négociable. Une sauvegarde en ligne peut être chiffrée par un ransomware en même temps que le reste du réseau.
En cas de compromission majeure de l'identité (comme un Golden Ticket), la seule solution fiable est souvent de reconstruire entièrement la forêt à partir d'une sauvegarde saine connue ou de repartir de zéro. Il est impossible de faire à nouveau confiance à une forêt dont le compte krbtgt a été compromis.
Conclusion : Le Devoir du Gardien
Nous avons parcouru un long chemin, de la simple cartographie du royaume à l'étude des stratégies de guerre les plus complexes. Nous avons vu que chaque décision, de la création d'une Unité d'Organisation à la configuration d'une politique de mot de passe, a une implication directe sur la sécurité. Le royaume Active Directory est un système complexe où la structure, la gestion et la sécurité sont indissociables.
La sécurité n'est pas un projet avec une date de fin ; c'est un processus continu. Le royaume n'est jamais définitivement "sécurisé". Il est maintenu dans un état de sécurité grâce à une vigilance constante, un audit régulier, une formation continue et une amélioration perpétuelle. Les menaces évoluent, et votre défense doit en faire de même.
La théorie est maintenant posée. La prochaine étape de votre voyage vers l'expertise est la pratique. Montez un laboratoire, créez des domaines, testez les GPO, exécutez les outils d'audit, et même, simulez des attaques pour comprendre la perspective de l'attaquant. Le gardien ne dort jamais ; il veille sur son royaume. Votre veille commence maintenant.
