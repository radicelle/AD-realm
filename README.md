Ce rapport est un guide détaillé sur la sécurisation d'Active Directory (AD), le présentant comme un "royaume numérique" où les administrateurs sont les gardiens. Il est structuré en quatre parties principales couvrant la cartographie du royaume, ses habitants et ses lois, l'authentification et le contrôle d'accès, et enfin les stratégies de défense contre les menaces.
Partie I : Cartographier le Royaume - Les Structures Fondamentales d'Active Directory
[cite_start]L'organisation d'un environnement Active Directory en domaines, arbres et forêts est une décision de sécurité fondamentale.
 * Le Domaine - La Capitale du Royaume
   * Le Service d'Annuaire : La Grande Bibliothèque du Royaume
     [cite_start]Active Directory est avant tout un service d'annuaire, agissant comme une gigantesque bibliothèque contenant des fiches détaillées sur chaque "objet" du royaume, tels que les comptes utilisateur, les comptes ordinateur, les groupes et les imprimantes. [cite_start]Cet annuaire centralisé permet aux administrateurs de gérer et sécuriser toutes les ressources réseau depuis un seul endroit.
   * Le Domaine : La Cité-État
     [cite_start]Un domaine est un regroupement logique d'objets (utilisateurs, ordinateurs, etc.) qui partagent la même base de données d'annuaire et sont soumis à un ensemble commun de règles. [cite_start]Il établit une frontière administrative claire, où les stratégies et les administrateurs du domaine gèrent tout ce qu'il contient, leurs pouvoirs s'arrêtant aux frontières du domaine.
   * Le Contrôleur de Domaine (DC) : Le Château Fort
     [cite_start]Le Contrôleur de Domaine (DC) est un serveur Windows qui héberge et protège la base de données Active Directory (ntds.dit). Son rôle est double et critique :
     * [cite_start]Authentification : Le DC vérifie l'identité d'un utilisateur lors d'une tentative de connexion en comparant le nom d'utilisateur et le mot de passe fournis avec les informations stockées dans sa base de données.
     * [cite_start]Autorisation : Une fois l'utilisateur authentifié, le DC informe les autres services du réseau de ce que l'utilisateur est autorisé à faire, en fonction de ses permissions et de ses appartenances à des groupes.
     [cite_start]Il est crucial d'avoir au moins deux Contrôleurs de Domaine par domaine pour garantir la résilience du royaume. [cite_start]Ces DC se répliquent constamment les informations entre eux grâce à un mécanisme de réplication multi-maître.
<!-- end list -->
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

 * Chapitre 2 : L'Arbre et la Forêt - Alliances et Empires
   * L'Arbre : Une Dynastie de Domaines
     [cite_start]Un arbre est une collection d'un ou plusieurs domaines partageant un espace de nom DNS contigu et hiérarchique. [cite_start]Une relation de confiance bidirectionnelle et transitive est automatiquement établie entre le parent et l'enfant, facilitant le partage de ressources.
   * La Forêt : La Frontière Ultime de l'Empire
     [cite_start]La forêt est le conteneur de plus haut niveau dans la structure logique d'Active Directory. [cite_start]C'est la frontière de sécurité, le périmètre le plus large au sein duquel les accès peuvent être gérés. [cite_start]Tous les domaines d'une même forêt partagent un schéma commun et un catalogue global (Global Catalog). [cite_start]Une relation de confiance bidirectionnelle et transitive est automatiquement établie entre tous les domaines de la forêt. [cite_start]La décision de créer une ou plusieurs forêts est une décision de sécurité majeure qui définit le niveau d'isolation entre les différentes entités d'une organisation.
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
[cite_start]La manière dont les objets sont créés, groupés et gérés, ainsi que la façon dont les politiques (GPO) sont appliquées, définit directement la surface d'attaque.
 * Chapitre 3 : Les Habitants du Royaume - Objets AD
   [cite_start]L'annuaire Active Directory est peuplé d'objets, qui se divisent en deux catégories principales:
   * [cite_start]Objets Feuilles (Leaf Objects) : Objets finaux qui ne peuvent pas contenir d'autres objets, tels que les comptes d'Utilisateurs, les comptes d'Ordinateurs et les objets Imprimantes.
   * [cite_start]Objets Conteneurs (Container Objects) : Objets qui peuvent en contenir d'autres pour organiser la structure, tels que les Domaines, les Unités d'Organisation (OU) et les Groupes.
   * L'Unité d'Organisation (OU) : Les Ministères et Provinces
     [cite_start]L'Unité d'Organisation (OU) est le conteneur le plus important et le plus flexible pour l'administration quotidienne. [cite_start]Les OU permettent de regrouper des objets logiquement pour refléter la structure de l'organisation. [cite_start]Leur puissance réside dans leur double fonction : lier des Stratégies de Groupe (GPO) et déléguer des droits administratifs.
   * Les Groupes : Les Guildes et Confréries
     [cite_start]Les groupes sont des collections d'objets (utilisateurs, ordinateurs, ou d'autres groupes) principalement utilisés pour gérer les permissions et les communications. [cite_start]Il existe deux types principaux:
     * [cite_start]Groupes de Sécurité : Leur but principal est d'assigner des permissions d'accès à des ressources partagées. [cite_start]Ils possèdent un identifiant de sécurité unique (SID) et peuvent être ajoutés aux Listes de Contrôle d'Accès (ACL).
     * [cite_start]Groupes de Distribution : Leur seul but est de créer des listes d'adresses e-mail pour faciliter la communication. [cite_start]Ils ne sont pas "activés pour la sécurité" et ne peuvent pas être utilisés pour accorder des permissions.
| Caractéristique | Groupe de Sécurité | Groupe de Distribution |
|---|---|---|
| Objectif Principal | Gérer les permissions et les accès aux ressources | Créer des listes d'e-mails pour la communication |
| Peut recevoir des permissions? | Oui (possède un SID, peut être dans une ACL) | Non (n'est pas un principal de sécurité) |
| Peut être utilisé pour envoyer des e-mails? | Oui | Oui |
| Cas d'usage typique | Donner l'accès au dossier "Comptabilité" | Envoyer une newsletter à toute l'entreprise |
| Considération de performance | Un trop grand nombre d'appartenances peut ralentir la connexion de l'utilisateur | Aucun impact sur la performance de connexion |
| Source: 76 |  |  |
 * Chapitre 4 : La Délégation de Contrôle - Nommer des Gouverneurs Locaux
   [cite_start]La délégation de contrôle est le mécanisme qui permet de confier des tâches administratives spécifiques sur des périmètres limités (généralement des OU) à d'autres utilisateurs ou groupes. [cite_start]L'outil principal pour cela est l'Assistant de Délégation de Contrôle, accessible par un clic droit sur une OU. [cite_start]La délégation est un outil puissant pour appliquer le principe du moindre privilège, mais doit être utilisée avec prudence pour éviter de créer des failles de sécurité. [cite_start]Il est impératif de ne jamais déléguer de contrôle sur les conteneurs par défaut (comme "Users") ou sur l'OU des contrôleurs de domaine.
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

 * Chapitre 5 : Les Stratégies de Groupe (GPO) - Le Code de Lois du Royaume
   [cite_start]Les Objets de Stratégie de Groupe (GPO) sont le principal mécanisme pour définir, déployer et imposer des règles et des configurations de manière centralisée. [cite_start]Les GPO permettent de déployer des logiciels, de mapper des lecteurs réseau et des imprimantes, de configurer des centaines de paramètres de sécurité, et bien plus encore.
   * [cite_start]L'Ordre d'Application (LSDOU) : Les politiques s'appliquent dans un ordre de précédence strict, chaque niveau pouvant écraser le précédent:
     * [cite_start]Local : Politique de la machine locale.
     * [cite_start]Site : Politique liée au site Active Directory.
     * [cite_start]Domaine : Politiques liées au domaine.
     * [cite_start]OU (Unité d'Organisation) : Politiques liées aux Unités d'Organisation, en descendant la hiérarchie.
   * [cite_start]La Règle d'Or : La politique la plus proche de l'objet l'emporte.
   * Bonnes Pratiques de Sécurité pour les GPO :
     * [cite_start]Ne jamais modifier les politiques par défaut ("Default Domain Policy" et "Default Domain Controllers Policy").
     * [cite_start]Structurer les OU de manière logique pour une gestion efficace des GPO.
     * [cite_start]Lier les GPO au plus haut niveau possible pour éviter la duplication et simplifier la gestion.
Partie III : La Garde Royale - Authentification et Contrôle d'Accès
[cite_start]La sécurité du royaume repose sur l'authentification (savoir qui entre) et l'autorisation (contrôler ce qu'ils peuvent faire).
 * Chapitre 6 : Kerberos - Le Protocole des Sceaux et des Laissez-passer
   [cite_start]Kerberos est le protocole d'authentification par défaut dans un environnement de domaine Active Directory, conçu pour être robuste sur des réseaux non-sûrs en évitant de transmettre les mots de passe en clair.
   * Les Acteurs :
     * [cite_start]Le Client : L'utilisateur qui souhaite accéder à un service.
     * [cite_start]Le Service : La ressource que le client veut utiliser.
     * Le KDC (Key Distribution Center) : Le bureau central des laissez-passer, situé sur le Contrôleur de Domaine (DC), avec deux guichets :
       * [cite_start]Authentication Service (AS) : Vérifie l'identité fondamentale.
       * [cite_start]Ticket Granting Service (TGS) : Délivre des laissez-passer pour des services spécifiques.
   * Le Flux d'Authentification en 3 Étapes :
     * AS-REQ / AS-REP (Obtenir un Laissez-passer Général) : Le client envoie une requête (AS-REQ) au KDC (AS) pour prouver son identité. [cite_start]Si l'identité est prouvée, l'AS renvoie une clé de session et un Ticket-Granting Ticket (TGT). [cite_start]Ce TGT est chiffré avec le hash du mot de passe du compte de service krbtgt et ne peut pas être lu ou modifié par le client.
     * [cite_start]TGS-REQ / TGS-REP (Échanger le Laissez-passer Général contre un Laissez-passer Spécifique) : Le client envoie une requête (TGS-REQ) au KDC (TGS) contenant son TGT et le nom du service visé. [cite_start]Le TGS vérifie la validité du TGT et génère un Ticket de Service (ST) spécifique au service, chiffré avec le secret du service.
     * [cite_start]AP-REQ / AP-REP (Présenter le Laissez-passer Spécifique au Service) : Le client présente son Ticket de Service au serveur de service dans une requête d'application (AP-REQ). [cite_start]Le service déchiffre le ticket pour vérifier l'identité et les permissions de l'utilisateur.
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

 * Chapitre 7 : Les Listes de Contrôle d'Accès (ACL) - Qui a la Clé de Quelle Porte?
   [cite_start]Une fois un utilisateur authentifié par Kerberos, les Listes de Contrôle d'Accès (ACL) déterminent ce qu'il peut faire. [cite_start]Une ACL est une liste de permissions attachée à chaque objet sécurisable dans Active Directory et sur le système de fichiers.
   * [cite_start]Les ACE (Access Control Entries) : L'ACL est composée d'une ou plusieurs Entrées de Contrôle d'Accès (ACE). [cite_start]Chaque ACE spécifie le principal de sécurité (utilisateur, groupe, ordinateur)[cite_start], le type d'accès (ex: Lire, Écrire)[cite_start], et si c'est une autorisation (Allow) ou une interdiction (Deny).
   * [cite_start]La Règle du "Deny" : Une règle de refus explicite l'emporte toujours sur une règle d'autorisation.
   * [cite_start]Gestion via les Groupes de Sécurité : Il est fondamental de ne jamais assigner de permissions directement à des utilisateurs individuels. [cite_start]La bonne pratique est de suivre le modèle AGDLP (Accounts go into Global groups, which go into Domain Local groups, which get Permissions).
Partie IV : L'Art de la Guerre - Sécuriser le Royaume Contre les Menaces
[cite_start]La compromission d'Active Directory est une chaîne d'attaque méthodique, et la défense doit rompre cette chaîne à plusieurs endroits.
 * Chapitre 8 : La Mentalité du Défenseur - Connaître son Ennemi
   * [cite_start]Une Surface d'Attaque Immense : Chaque compte utilisateur, ordinateur, GPO mal configurée, ou compte de service avec permissions excessives est une porte potentielle pour un attaquant. [cite_start]Les services réseau exposés par un contrôleur de domaine sont autant de vecteurs d'attaque possibles.
   * Les Objectifs de l'Attaquant :
     * [cite_start]Vol d'Identifiants (Credential Theft) : Obtenir des informations d'identification valides.
     * [cite_start]Escalade de Privilèges (Privilege Escalation) : Transformer un accès limité en un accès administrateur.
     * [cite_start]Mouvement Latéral (Lateral Movement) : Se déplacer d'un ordinateur à un autre au sein du réseau.
     * [cite_start]Persistance : Établir un accès durable et discret au réseau.
 * Chapitre 9 : Les Attaques Courantes Décortiquées
   * [cite_start]Pass-the-Hash (PtH) : L'attaquant vole l'empreinte (hash) du mot de passe d'un utilisateur de la mémoire vive d'une machine compromise, et l'utilise pour s'authentifier à d'autres ressources sans connaître le mot de passe en clair.
   * Kerberoasting : Un attaquant demande un Ticket de Service (ST) pour un service avec un SPN enregistré dans AD. Ce ticket est partiellement chiffré avec le hash du mot de passe du compte de service. [cite_start]L'attaquant récupère le ticket et tente de le craquer hors ligne, exploitant souvent des mots de passe faibles des comptes de service.
   * [cite_start]Golden & Silver Tickets : Les Sceaux Royaux Falsifiés : Attaques de post-exploitation nécessitant des secrets critiques déjà obtenus.
     * Golden Ticket : Falsification du TGT (laissez-passer général) en utilisant le hash du compte krbtgt. [cite_start]Permet à l'attaquant de créer un TGT pour n'importe qui, avec n'importe quels privilèges et une longue durée de validité, donnant un accès total et persistant à l'ensemble du domaine.
     * Silver Ticket : Falsification du Ticket de Service (ST) en utilisant le hash du mot de passe d'un compte de service spécifique. [cite_start]L'accès est limité au service spécifique, mais l'attaque est très discrète et difficile à détecter car elle ne nécessite aucune communication avec le KDC une fois le ticket forgé.
| Caractéristique | Golden Ticket Attack | Silver Ticket Attack |
|---|---|---|
| Ticket Falsifié | TGT (Ticket-Granting Ticket) | TGS/ST (Ticket-Granting Service) |
| Secret Requis | Hash NTLM du compte KRBTGT | Hash NTLM du compte de service cible |
| Portée de l'Accès | Toute la forêt/domaine (illimité) | Uniquement le service spécifique sur un serveur |
| Interaction avec le DC | L'attaquant utilise le TGT forgé pour demander des TGS légitimes au DC. | Aucune interaction avec le DC une fois le ticket forgé. L'authentification se fait directement auprès du service. |
| Persistance | Très élevée (tant que le mot de passe KRBTGT n'est pas changé 2 fois) | Élevée (tant que le mot de passe du service n'est pas changé) |
| Difficulté de Détection | Difficile, mais génère du trafic vers le DC (demandes de TGS) | Très difficile, car il n'y a pas de trafic anormal vers le DC. La détection doit se faire sur le serveur de service. |
| Source: 204 |  |  |
 * Chapitre 10 : La Stratégie de Défense en Profondeur
   [cite_start]La sécurisation d'Active Directory repose sur le principe de "défense en profondeur".
   * [cite_start]Le Principe du Moindre Privilège (PoLP) : Aucun utilisateur, service ou système ne doit avoir plus de droits que ce qui est strictement nécessaire. [cite_start]Cela implique de réduire le nombre de membres des groupes à hauts privilèges, d'utiliser des comptes séparés pour les tâches administratives et quotidiennes, et de ne jamais utiliser un compte à privilèges sur une machine moins sécurisée.
   * [cite_start]Le Modèle de Tiers (Tier Model) : Ce modèle divise l'infrastructure en niveaux pour contenir les attaques:
     * [cite_start]Tier 0 : Le Cœur du Royaume, contenant les actifs les plus critiques (Contrôleurs de Domaine, comptes à hauts privilèges, compte krbtgt).
     * [cite_start]Tier 1 : Les Infrastructures Clés, comprenant les serveurs d'entreprise et les applications critiques.
     * [cite_start]Tier 2 : Les Postes de Travail, incluant les ordinateurs des utilisateurs finaux.
     * [cite_start]La Règle Fondamentale : Un niveau inférieur ne peut jamais contrôler ou administrer un niveau supérieur.
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
   [cite_start]La protection des DC est non négociable. [cite_start]Cela inclut la sécurité physique[cite_start], l'interdiction d'accès à Internet depuis un DC[cite_start], l'application rigoureuse des correctifs de sécurité[cite_start], l'utilisation de pare-feu basés sur l'hôte[cite_start], l'interdiction des connexions RDP sauf depuis des stations de travail d'administration dédiées (PAW)[cite_start], et la désactivation des services non essentiels.
 * Chapitre 11 : L'Arsenal du Défenseur - Bonnes Pratiques et Outils
   [cite_start]La défense nécessite des outils et des processus robustes.
   * [cite_start]Audit et Surveillance Continus : Activer les stratégies d'audit avancées de Windows et utiliser des outils comme PingCastle[cite_start], BloodHound[cite_start], et Microsoft Defender for Identity [cite_start]pour détecter les comportements anormaux.
   * [cite_start]Gestion des Comptes à Privilèges (PAM) : Mettre en place des solutions pour un accès "juste-à-temps" (JIT), où les privilèges sont accordés pour une dur
