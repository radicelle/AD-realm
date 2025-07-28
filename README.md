[cite_start]Ce rapport vous invite à un voyage au cœur du "Royaume Numérique" d'Active Directory (AD), où vous, futur administrateur, deviendrez le gardien des clés. [cite_start]Loin d'être un simple logiciel, AD est la pierre angulaire de l'infrastructure informatique pour 90 % des entreprises mondiales. [cite_start]Comprendre sa structure est important, mais maîtriser sa sécurité est une responsabilité absolue. [cite_start]Ce guide vous transformera d'un simple visiteur en un gardien expert, en décomposant sa complexité et en insistant sur l'art de sa défense.
Partie I : Cartographier le Royaume - Les Structures Fondamentales d'Active Directory
[cite_start]L'organisation de votre royaume numérique, avec ses provinces et ses frontières, est la première et l'une des plus fondamentales décisions de sécurité. [cite_start]Une conception malavisée de cette structure peut introduire des failles de sécurité systémiques avant même la création du premier utilisateur.
Chapitre 1 : Le Domaine - La Capitale du Royaume
[cite_start]Au cœur de tout royaume se trouve un registre central.
 * Le Service d'Annuaire : La Grande Bibliothèque du Royaume
   [cite_start]Active Directory est, avant tout, un service d'annuaire. [cite_start]Imaginez une gigantesque bibliothèque qui contient des fiches détaillées sur chaque "objet" du royaume : chaque citoyen (compte utilisateur), chaque chariot (compte ordinateur), chaque guilde (groupe), et même chaque imprimante. [cite_start]Cet annuaire centralisé permet aux administrateurs de gérer, d'organiser et de sécuriser toutes les ressources du réseau à partir d'un seul endroit.
 * Le Domaine : La Cité-État
   [cite_start]Un domaine est un groupe logique d'objets (utilisateurs, ordinateurs, etc.) qui partagent la même base de données d'annuaire et sont soumis à un ensemble commun de règles. [cite_start]C'est la capitale de votre territoire, une cité-état administrative. [cite_start]Il définit une frontière administrative claire, où les lois (stratégies) et les administrateurs de ce domaine gèrent tout ce qui s'y trouve, et leurs pouvoirs s'arrêtent aux frontières du domaine.
 * Le Contrôleur de Domaine (DC) : Le Château Fort
   [cite_start]Si le domaine est la capitale, le Contrôleur de Domaine (DC) est son château fort. [cite_start]Le DC est un serveur Windows qui héberge et protège la "grande bibliothèque" (la base de données Active Directory, ntds.dit). [cite_start]Son rôle est double et critique:
   * [cite_start]Authentification : Il agit comme le garde à l'entrée du château, vérifiant l'identité des utilisateurs qui tentent de se connecter.
   * [cite_start]Autorisation : Une fois l'utilisateur authentifié, le DC informe les autres services du réseau de ce que cet utilisateur a le droit de faire.
   [cite_start]Pour garantir la résilience du royaume, il est crucial d'avoir au moins deux Contrôleurs de Domaine par domaine. [cite_start]Ces DC se répliquent constamment les informations entre eux grâce à un mécanisme de réplication multi-maître.
<!-- end list -->
'''mermaid
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
'''
Chapitre 2 : L'Arbre et la Forêt - Alliances et Empires
[cite_start]Un royaume peut s'étendre et donner naissance à de nouvelles provinces.
 * L'Arbre : Une Dynastie de Domaines
   [cite_start]Dans Active Directory, un arbre est une collection d'un ou plusieurs domaines qui partagent un espace de nom DNS contigu et hiérarchique. [cite_start]Si MonRoyaume.com est le domaine parent, alors Ventes.MonRoyaume.com et RH.MonRoyaume.com sont des domaines enfants. [cite_start]Une relation de confiance bidirectionnelle et transitive est automatiquement établie entre le parent et l'enfant, facilitant le partage de ressources.
 * La Forêt : La Frontière Ultime de l'Empire
   [cite_start]La forêt est le conteneur de plus haut niveau dans la structure logique d'Active Directory. [cite_start]C'est un ensemble d'un ou plusieurs arbres qui ne partagent pas nécessairement le même espace de nom.
   * [cite_start]Importance Critique de la Forêt : La forêt est la frontière de sécurité ultime. [cite_start]C'est le périmètre le plus large au sein duquel les accès peuvent être gérés.
   * [cite_start]Éléments Partagés : Tous les domaines d'une même forêt partagent deux éléments fondamentaux:
     * [cite_start]Un Schéma Commun : Le plan de construction qui définit ce qu'est un "objet" est identique pour toute la forêt.
     * [cite_start]Un Catalogue Global (Global Catalog) : Un index partiel contenant les informations les plus importantes sur chaque objet de la forêt, permettant des recherches rapides à travers tous les domaines.
   * [cite_start]Une relation de confiance bidirectionnelle et transitive est automatiquement établie entre tous les domaines de la forêt. [cite_start]Cela signifie qu'un compte d'un domaine peut potentiellement accéder à une ressource dans un autre domaine de la même forêt si les permissions appropriées sont accordées.
<!-- end list -->
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
[cite_start]Une fois la carte du royaume établie, il est temps de s'intéresser à ses habitants et aux lois qui les régissent. [cite_start]La manière dont les objets sont créés, groupés et gérés, ainsi que la façon dont les politiques (GPO) sont appliquées, définit directement la surface d'attaque.
Chapitre 3 : Les Habitants du Royaume - Objets AD
[cite_start]L'annuaire Active Directory est peuplé d'objets, qui sont les entités représentant les ressources du réseau.
 * Objets Feuilles (Leaf Objects) : Ce sont les objets finaux, comme les citoyens et leurs biens. Ils ne peuvent pas contenir d'autres objets. [cite_start]Les exemples les plus courants sont les comptes d'Utilisateurs, les comptes d'Ordinateurs et les objets Imprimantes.
 * Objets Conteneurs (Container Objects) : Ce sont des objets qui peuvent en contenir d'autres, servant à organiser la structure. [cite_start]Les exemples incluent les Domaines, les Unités d'Organisation (OU) et les Groupes.
 * L'Unité d'Organisation (OU) : Les Ministères et Provinces
   [cite_start]L'Unité d'Organisation (OU) est le conteneur le plus important et le plus flexible pour l'administration quotidienne. [cite_start]Pensez aux OU comme aux ministères (Ventes, Finance, R&D) ou aux provinces (Paris, Lyon) de votre royaume. [cite_start]Elles vous permettent de regrouper des objets (principalement des utilisateurs et des ordinateurs) de manière logique pour refléter la structure de votre organisation.
   * [cite_start]Rôle Fondamental : La puissance des OU réside dans leur double fonction:
     * [cite_start]Lier des Stratégies de Groupe (GPO) : Pour appliquer des configurations spécifiques à un ensemble d'utilisateurs ou d'ordinateurs.
     * [cite_start]Déléguer des droits administratifs : Pour donner des permissions de gestion limitées à des équipes spécifiques.
 * Les Groupes : Les Guildes et Confréries
   [cite_start]Les groupes sont des collections d'objets (utilisateurs, ordinateurs, ou même d'autres groupes) principalement utilisés pour gérer les permissions et les communications de manière efficace. [cite_start]Il est crucial de comprendre la différence entre les deux types principaux.
   * Groupes de Sécurité :
     * [cite_start]Métaphore : Ce sont les détenteurs de clés du royaume. [cite_start]Au lieu de donner une clé individuelle pour la salle du trésor à chaque membre de la garde royale, vous donnez une seule clé au "Groupe de la Garde Royale".
     * [cite_start]Fonction : Leur but principal est d'assigner des permissions d'accès à des ressources partagées (fichiers, dossiers, imprimantes). [cite_start]Ils possèdent un identifiant de sécurité unique (SID) et peuvent être ajoutés aux Listes de Contrôle d'Accès (ACL) des ressources. [cite_start]Ils peuvent également être utilisés comme liste de distribution d'e-mails.
   * Groupes de Distribution :
     * [cite_start]Métaphore : Ce sont des listes de diffusion pour les messagers du roi, permettant d'envoyer un décret à tous les nobles d'un coup.
     * [cite_start]Fonction : Leur seul et unique but est de créer des listes d'adresses e-mail pour faciliter la communication. [cite_start]Ils ne sont pas "activés pour la sécurité", ce qui signifie qu'ils n'ont pas de SID et ne peuvent PAS être utilisés pour accorder des permissions.
[cite_start]Le choix entre ces deux types de groupes est une décision de gestion fondamentale.
| Caractéristique | Groupe de Sécurité | Groupe de Distribution |
|---|---|---|
| Objectif Principal | Gérer les permissions et les accès aux ressources | Créer des listes d'e-mails pour la communication |
| Peut recevoir des permissions? | Oui (possède un SID, peut être dans une ACL) | Non (n'est pas un principal de sécurité) |
| Peut être utilisé pour envoyer des e-mails? | Oui | Oui |
| Cas d'usage typique | Donner l'accès au dossier "Comptabilité" | Envoyer une newsletter à toute l'entreprise |
| Considération de performance | Un trop grand nombre d'appartenances peut ralentir la connexion de l'utilisateur | Aucun impact sur la performance de connexion |
| Source: 76 |  |  |
Chapitre 4 : La Délégation de Contrôle - Nommer des Gouverneurs Locaux
[cite_start]Il est à la fois dangereux et inefficace que seuls les "Admins du Domaine" puissent tout faire dans le royaume. [cite_start]La délégation de contrôle est le mécanisme qui permet de confier des tâches administratives spécifiques sur des périmètres limités (généralement des OU) à d'autres utilisateurs ou groupes.
 * [cite_start]Comment ça marche? L'outil principal pour cela est l'Assistant de Délégation de Contrôle, accessible par un clic droit sur une OU. [cite_start]Un administrateur peut y accorder des permissions granulaires prédéfinies.
 * [cite_start]Focus Sécurité : La délégation est un outil puissant pour appliquer le principe du moindre privilège, mais elle doit être maniée avec une extrême prudence. [cite_start]Une délégation mal configurée peut créer des failles de sécurité béantes. [cite_start]Il est impératif de ne jamais déléguer de contrôle sur les conteneurs par défaut (comme "Users") ou sur l'OU des contrôleurs de domaine.
<!-- end list -->
graph TD
    A --"Délègue la tâche via l'Assistant"--> B(OU 'Ventes')
    subgraph B
        C[Utilisateur 'Jean Vente']
        D[Ordinateur 'PC-Jean']
    end
    E --"Reçoit la permission sur OU 'Ventes'"--> B
    F --"Peut maintenant réinitialiser le mot de passe de"--> C
    F -.->|N'a aucun droit sur| G(OU 'Finance')

Chapitre 5 : Les Stratégies de Groupe (GPO) - Le Code de Lois du Royaume
[cite_start]Les Objets de Stratégie de Groupe (GPO) sont le principal mécanisme pour définir, déployer et imposer des règles et des configurations de manière centralisée. [cite_start]Les GPO sont le code de lois du royaume.
 * Ce qu'on peut faire avec les GPO : La portée des GPO est immense. [cite_start]Elles permettent de déployer des logiciels, de mapper des lecteurs réseau, de configurer des centaines de paramètres de sécurité (règles de pare-feu, restrictions sur les ports USB, politique d'audit), de définir des fonds d'écran, et de restreindre l'accès à des parties du système d'exploitation.
 * [cite_start]L'Ordre d'Application (LSDOU) : C'est un concept fondamental pour comprendre et dépanner les GPO. [cite_start]Les politiques s'appliquent dans un ordre de précédence strict, chaque niveau pouvant écraser le précédent:
   * [cite_start]Local : D'abord, la politique de la machine locale elle-même.
   * [cite_start]Site : Ensuite, la politique liée au site Active Directory.
   * [cite_start]Domaine : Puis, la ou les politiques liées au domaine.
   * [cite_start]OU (Unité d'Organisation) : Enfin, les politiques liées aux Unités d'Organisation, en commençant par l'OU la plus haute dans la hiérarchie et en descendant jusqu'à l'OU qui contient l'objet.
 * [cite_start]La Règle d'Or : La politique la plus proche de l'objet l'emporte.
 * Bonnes Pratiques de Sécurité pour les GPO :
   * [cite_start]Ne modifiez JAMAIS les politiques par défaut[span_74](start_span)! [cite_start]La "Default Domain Policy" et la "Default Domain Controllers Policy" sont des objets critiques et doivent être utilisées uniquement pour des politiques spécifiques. [cite_start]Pour toute autre configuration, créez de nouvelles GPO.
   * [cite_start]Structurez vos OU de manière logique : Une bonne structure d'OU est indispensable pour une gestion efficace des GPO.
   * [cite_start]Liez les GPO au plus haut niveau possible : Pour éviter la duplication et simplifier la gestion, appliquez les GPO communes à la racine d'une arborescence d'OU.
Partie III : La Garde Royale - Authentification et Contrôle d'Accès
[cite_start]Un royaume sécurisé repose sur deux piliers : savoir qui entre (l'authentification) et contrôler ce qu'ils peuvent faire une fois à l'intérieur (l'autorisation). [cite_start]Ces deux concepts sont distincts mais intrinsèquement liés.
Chapitre 6 : Kerberos - Le Protocole des Sceaux et des Laissez-passer
[cite_start]Kerberos est le protocole d'authentification par défaut dans un environnement de domaine Active Directory. [cite_start]Il a été conçu pour être robuste sur des réseaux considérés comme non-sûrs, en évitant de transmettre les mots de passe en clair.
 * Les Acteurs :
   * [cite_start]Le Client (vous) : Le citoyen qui souhaite accéder à un service.
   * [cite_start]Le Service (par exemple, un serveur de fichiers) : La ressource que vous voulez utiliser.
   * [cite_start]Le KDC (Key Distribution Center) : Le bureau central des laissez-passer, une entité de confiance située sur le Contrôleur de Domaine (DC). [cite_start]Ce bureau a deux guichets:
     * [cite_start]Authentication Service (AS) : Le premier guichet, qui vérifie votre identité fondamentale.
     * [cite_start]Ticket Granting Service (TGS) : Le second guichet, qui délivre des laissez-passer pour des services spécifiques.
 * Le Flux d'Authentification en 3 Étapes :
   * [cite_start]AS-REQ / AS-REP (Obtenir un Laissez-passer Général) : Le client se présente au guichet AS du KDC et envoie une requête (AS-REQ). [cite_start]Pour prouver son identité, le client envoie un timestamp chiffré avec une clé dérivée de son mot de passe secret. [cite_start]Si le KDC parvient à déchiffrer le timestamp, l'identité est prouvée. [cite_start]L'AS renvoie alors une clé de session et un Ticket-Granting Ticket (TGT). [cite_start]Ce TGT est un laissez-passer général, valable pour une durée limitée, chiffré avec un secret que seul le KDC connaît : le hash du mot de passe du compte de service krbtgt. [cite_start]Le client ne peut ni lire ni modifier son TGT.
   * [cite_start]TGS-REQ / TGS-REP (Échanger le Laissez-passer Général contre un Laissez-passer Spécifique) : Le client envoie une requête (TGS-REQ) au guichet TGS du KDC, contenant son TGT et le nom du service visé (son Service Principal Name, ou SPN). [cite_start]Le TGS déchiffre le TGT, vérifie sa validité, puis génère un Ticket de Service (ST). [cite_start]Ce ticket est un laissez-passer spécifique, valable uniquement pour le service demandé, et chiffré avec le secret du service.
   * [cite_start]AP-REQ / AP-REP (Présenter le Laissez-passer Spécifique au Service) : Le client présente son Ticket de Service au service dans une requête d'application (AP-REQ). [cite_start]Le service utilise son propre secret pour déchiffrer le ticket. [cite_start]S'il parvient à déchiffrer le ticket, il sait qu'il est authentique et a été émis par le KDC, et il accorde l'accès.
<!-- end list -->
sequenceDiagram
    participant Client
    participant KDC_AS as KDC (Guichet AS)
    participant KDC_TGS as KDC (Guichet TGS)
    participant Service
    Client->>KDC_AS: 1. AS-REQ: Je suis Bob, je veux un TGT.
    (Preuve chiffrée avec mon secret)
    KDC_AS-->>Client: 2. AS-REP: Voici ton TGT (chiffré avec secret KDC) et une clé de session.
    Client->>KDC_TGS: 3. TGS-REQ: Voici mon TGT, je veux un ticket pour le Service X.
    KDC_TGS-->>Client: 4. TGS-REP: Voici ton Ticket de Service pour X (chiffré avec secret de X).
    Client->>Service: 5. AP-REQ: Voici mon Ticket de Service pour toi.
    Service-->>Client: 6. AP-REP (optionnel): Accès accordé/refusé.

Chapitre 7 : Les Listes de Contrôle d'Accès (ACL) - Qui a la Clé de Quelle Porte?
[cite_start]Une fois qu'un utilisateur est authentifié par Kerberos, ce sont les Listes de Contrôle d'Accès (ACL) qui déterminent ce qu'il peut réellement faire. [cite_start]Une ACL est une liste de permissions attachée à chaque objet sécurisable dans Active Directory et sur le système de fichiers.
 * [cite_start]Les ACE (Access Control Entries) : L'ACL est composée d'une ou plusieurs Entrées de Contrôle d'Accès (ACE). [cite_start]Chaque ACE spécifie trois choses:
   * [cite_start]Principal de Sécurité : Pour qui cette règle s'applique (SID d'un utilisateur, d'un groupe de sécurité ou d'un ordinateur).
   * [cite_start]Permission : Quel type d'accès est concerné (par exemple, Lire, Écrire, Contrôle total).
   * [cite_start]Type : S'agit-il d'une autorisation (Allow) ou d'une interdiction (Deny).
 * [cite_start]La Règle du "Deny" : Une règle de refus explicite (Deny) l'emporte toujours sur une règle d'autorisation (Allow).
 * [cite_start]Gestion via les Groupes de Sécurité : Il est fondamental de ne jamais assigner de permissions directement à des utilisateurs individuels. [cite_start]La bonne pratique consiste à suivre le modèle AGDLP (Accounts go into Global groups, which go into Domain Local groups, which get Permissions).
Partie IV : L'Art de la Guerre - Sécuriser le Royaume Contre les Menaces
[cite_start]Comprendre la structure et les lois du royaume est une chose ; le défendre en est une autre. [cite_start]La compromission d'Active Directory n'est pas un événement singulier, mais une chaîne d'attaque méthodique. [cite_start]La défense doit donc être pensée comme la rupture de cette chaîne à plusieurs endroits.
Chapitre 8 : La Mentalité du Défenseur - Connaître son Ennemi
[cite_start]Pour défendre le royaume, il faut penser comme un assaillant.
 * [cite_start]Une Surface d'Attaque Immense : La surface d'attaque d'Active Directory est partout. [cite_start]Chaque compte utilisateur, chaque ordinateur, chaque GPO mal configurée, chaque compte de service avec des permissions excessives est une porte potentielle.
 * Les Objectifs de l'Attaquant :
   * [cite_start]Vol d'Identifiants (Credential Theft) : L'objectif premier est d'obtenir des informations d'identification valides.
   * [cite_start]Escalade de Privilèges (Privilege Escalation) : L'attaquant cherche à transformer un accès limité en un accès administrateur.
   * [cite_start]Mouvement Latéral (Lateral Movement) : Utiliser un compte compromis pour se déplacer d'un ordinateur à un autre au sein du réseau.
   * [cite_start]Persistance : L'objectif ultime est d'établir un accès durable et discret au réseau.
Chapitre 9 : Les Attaques Courantes Décortiquées
[cite_start]Pour contrer ces tactiques, il faut les comprendre en détail.
 * Pass-the-Hash (PtH)
   * [cite_start]Métaphore : Au lieu de voler la clé physique d'une porte, l'attaquant vole une empreinte parfaite de la clé.
   * [cite_start]Comment ça marche : Un attaquant compromet une machine et utilise un outil comme Mimikatz pour extraire de la mémoire vive les hashes NTLM des utilisateurs connectés. [cite_start]Armé de ces hashes, il peut se connecter à d'autres serveurs ou postes de travail en se faisant passer pour ces utilisateurs, sans jamais avoir besoin de connaître ou de craquer leurs mots de passe en clair.
 * Kerberoasting
   * [cite_start]Métaphore : Certains serviteurs du royaume (les comptes de service utilisés par les applications) ont des clés pour des portes de service importantes, souvent avec des mots de passe très simples. [cite_start]Un espion (l'attaquant) peut demander au bureau des laissez-passer une copie du cadenas de cette porte de service, puis l'emporter chez lui pour essayer de le craquer par force brute.
   * [cite_start]Comment ça marche : N'importe quel utilisateur authentifié du domaine peut demander un Ticket de Service (ST/TGS) pour un service qui a un Service Principal Name (SPN) enregistré dans AD. [cite_start]Ce ticket est partiellement chiffré avec le hash du mot de passe du compte de service. [cite_start]L'attaquant récupère ce ticket et tente de le craquer "hors ligne". [cite_start]Cette attaque a un taux de réussite élevé car les mots de passe des comptes de service sont souvent faibles.
 * Golden & Silver Tickets : Les Sceaux Royaux Falsifiés
   [cite_start]Ce sont des attaques de post-exploitation qui supposent que l'attaquant a déjà obtenu des secrets critiques.
   * Golden Ticket :
     * [cite_start]Métaphore : C'est la falsification du sceau royal suprême, fabriquée à partir du secret le plus gardé : le hash du mot de passe du compte krbtgt.
     * [cite_start]Impact : Si un attaquant vole ce secret, il peut créer un TGT (le laissez-passer général) pour n'importe qui, lui accorder n'importe quels privilèges (Admin du Domaine), et lui donner une durée de validité de plusieurs années. [cite_start]Il devient le roi de l'ombre, capable d'accéder à tout, partout, sans être détecté. [cite_start]C'est le "game over" pour la sécurité de la forêt AD.
   * Silver Ticket :
     * [cite_start]Métaphore : C'est la falsification du sceau d'un gouverneur local (un service spécifique). [cite_start]L'attaquant vole le hash du mot de passe de ce compte de service et l'utilise pour forger un Ticket de Service (ST) valide pour accéder à ce service uniquement.
     * [cite_start]Impact : L'accès est limité à un service spécifique, mais l'attaque est beaucoup plus discrète. [cite_start]Elle ne nécessite aucune communication avec le KDC une fois le ticket forgé, ce qui la rend très difficile à détecter.
[cite_start]Le tableau suivant résume les différences critiques entre ces deux attaques.
| Caractéristique | Golden Ticket Attack | Silver Ticket Attack |
|---|---|---|
| Ticket Falsifié | TGT (Ticket-Granting Ticket) | TGS/ST (Ticket-Granting Service) |
| Secret Requis | Hash NTLM du compte KRBTGT | Hash NTLM du compte de service cible |
| Portée de l'Accès | Toute la forêt/domaine (illimité) | Uniquement le service spécifique sur un serveur |
| Interaction avec le DC | L'attaquant utilise le TGT forgé pour demander des TGS légitimes au DC. | Aucune interaction avec le DC une fois le ticket forgé. L'authentification se fait directement auprès du service. |
| Persistance | Très élevée (tant que le mot de passe KRBTGT n'est pas changé 2 fois) | Élevée (tant que le mot de passe du service n'est pas changé) |
| Difficulté de Détection | Difficile, mais génère du trafic vers le DC (demandes de TGS) | Très difficile, car il n'y a pas de trafic anormal vers le DC. La détection doit se faire sur le serveur de service. |
| Source: 204 |  |  |
Chapitre 10 : La Stratégie de Défense en Profondeur
[cite_start]Une forteresse imprenable ne repose pas sur un seul mur, mais sur de multiples couches de défense. [cite_start]La sécurisation d'Active Directory suit ce même principe de "défense en profondeur".
 * Le Principe du Moindre Privilège (PoLP) : La Règle d'Or
   [cite_start]C'est le concept le plus fondamental de la cybersécurité : aucun utilisateur, service ou système ne doit avoir plus de droits que ce qui est strictement nécessaire pour accomplir sa tâche. Dans Active Directory, cela se traduit par des actions concrètes :
   * [cite_start]Auditer et réduire drastiquement le nombre de membres des groupes à hauts privilèges (Admins du Domaine, Admins de l'Entreprise, Admins du Schéma) au strict minimum.
   * [cite_start]Utiliser des comptes séparés pour les tâches administratives et les tâches quotidiennes (email, navigation web). [cite_start]Un administrateur ne devrait jamais consulter ses emails avec son compte à privilèges.
   * [cite_start]Ne jamais utiliser un compte à privilèges pour se connecter à une machine moins sécurisée. [cite_start]Un administrateur de domaine ne doit pas se connecter à un poste de travail standard.
 * Le Modèle de Tiers (Tier Model) : Cloisonner pour Mieux Régner
   [cite_start]Recommandé par Microsoft et des agences comme l'ANSSI[cite_start], ce modèle administratif divise l'infrastructure en niveaux (Tiers) pour contenir les attaques et empêcher les mouvements latéraux et l'escalade de privilèges.
   * Tier 0 : Le Cœur du Royaume. [cite_start]Contient les actifs les plus critiques, les "clés du royaume". [cite_start]La compromission du Tier 0 signifie la compromission de tout le royaume. [cite_start]Il inclut : les Contrôleurs de Domaine, les comptes à hauts privilèges (Admins du Domaine, etc.), le compte krbtgt, et les systèmes qui gèrent ces actifs.
   * Tier 1 : Les Infrastructures Clés. [cite_start]Contient les serveurs d'entreprise et les applications critiques (serveurs de fichiers, bases de données, serveurs d'applications) et les comptes qui les administrent.
   * Tier 2 : Les Postes de Travail. [cite_start]Contient les ordinateurs des utilisateurs finaux et les comptes utilisateurs standards.
   * [cite_start]La Règle Fondamentale : Un niveau inférieur ne peut JAMAIS contrôler ou administrer un niveau supérieur. [cite_start]Ce cloisonnement strict empêche un attaquant qui a compromis un poste de travail (Tier 2) de se déplacer facilement pour atteindre un DC (Tier 0).
<!-- end list -->
graph TD
    subgraph Tier 0
        A
        B
    end
    subgraph Tier 1
        C
        D
    end
    subgraph Tier 2
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

 * Sécuriser les Contrôleurs de Domaine : Protéger le Château Fort
   [cite_start]Les DC sont la cible ultime. [cite_start]Leur protection est non négociable.
   * [cite_start]Sécurité Physique : Les DC doivent être physiquement sécurisés dans des salles de serveurs verrouillées, avec un accès contrôlé.
   * Sécurité Logique :
     * [cite_start]Aucun accès à Internet ne doit être autorisé depuis un DC. [cite_start]La navigation web sur un DC est une faute professionnelle grave.
     * [cite_start]Appliquer les derniers correctifs de sécurité de manière rigoureuse et rapide.
     * [cite_start]Utiliser des pare-feu basés sur l'hôte pour restreindre les communications aux seuls ports et protocoles strictement nécessaires.
     * [cite_start]Interdire la connexion RDP aux DC, sauf depuis des stations de travail d'administration dédiées et sécurisées (PAW - Privileged Access Workstation).
     * [cite_start]Désactiver les services non essentiels comme le spouleur d'impression (Print Spooler).
Chapitre 11 : L'Arsenal du Défenseur - Bonnes Pratiques et Outils
[cite_start]La défense est un travail actif qui nécessite des outils et des processus robustes.
 * [cite_start]Audit et Surveillance Continus : Vous ne pouvez pas protéger ce que vous ne voyez pas. [cite_start]Activez les stratégies d'audit avancées de Windows pour journaliser les événements de sécurité critiques.
   * Utilisez des outils d'audit et de détection. [cite_start]PingCastle est un excellent outil pour réaliser un audit rapide de la maturité de sécurité de votre AD. [cite_start]BloodHound utilise la théorie des graphes pour visualiser les chemins d'attaque et les relations de privilèges complexes.
   * [cite_start]Des solutions comme Microsoft Defender for Identity sont conçues pour détecter les comportements anormaux et les attaques en temps réel.
 * [cite_start]Gestion des Comptes à Privilèges (PAM - Privileged Access Management) : Au lieu de donner des droits administratifs permanents, mettez en place des solutions pour un accès "juste-à-temps" (JIT).
 * Gestion des Comptes de Service :
   * [cite_start]Chaque fois que possible, utilisez des comptes de service gérés par le groupe (gMSA). [cite_start]Leur mot de passe, long et complexe, est géré et changé automatiquement par Active Directory, ce qui neutralise les attaques par Kerberoasting.
   * [cite_start]Pour les comptes de service manuels, utilisez des mots de passe extrêmement longs (plus de 25 caractères) et complexes, et mettez en place un processus pour les faire tourner régulièrement.
 * Plan de Reprise Après Sinistre (Disaster Recovery) :
   * [cite_start]Avoir des sauvegardes régulières, testées et, surtout, hors ligne (air-gapped) de vos contrôleurs de domaine est non-négociable. [cite_start]Une sauvegarde en ligne peut être chiffrée par un ransomware en même temps que le reste du réseau.
   * [cite_start]En cas de compromission majeure de l'identité (comme un Golden Ticket), la seule solution fiable est souvent de reconstruire entièrement la forêt à partir d'une sauvegarde saine connue ou de repartir de zéro. [cite_start]Il est impossible de faire à nouveau confiance à une forêt dont le compte krbtgt a été compromis.
Conclusion : Le Devoir du Gardien
[cite_start]Nous avons parcouru un long chemin, de la simple cartographie du royaume à l'étude des stratégies de guerre les plus complexes. [cite_start]Nous avons vu que chaque décision, de la création d'une Unité d'Organisation à la configuration d'une politique de mot de passe, a une implication directe sur la sécurité. [cite_start]Le royaume Active Directory est un système complexe où la structure, la gestion et la sécurité sont indissociables.
[cite_start]La sécurité n'est pas un projet avec une date de fin ; c'est un processus continu. [cite_start]Le royaume n'est jamais définitivement "sécurisé". [cite_start]Il est maintenu dans un état de sécurité grâce à une vigilance constante, un audit régulier, une formation continue et une amélioration perpétuelle. [cite_start]Les menaces évoluent, et votre défense doit en faire de même.
[cite_start]La théorie est maintenant posée. [cite_start]La prochaine étape de votre voyage vers l'expertise est la pratique. [cite_start]Montez un laboratoire, créez des domaines, testez les GPO, exécutez les outils d'audit, et même, simulez des attaques pour comprendre la perspective de l'attaquant. Le gardien ne dort jamais ; il veille sur son royaume. [cite_start]Votre veille commence maintenant.
