Absolument ! Voici le document formaté pour être parfaitement compatible avec le README de GitHub, avec une attention particulière pour que les en-têtes, les tableaux et surtout les diagrammes s'affichent correctement.
Le Royaume Active Directory - De Novice à Gardien des Clés
Introduction : Bienvenue dans le Royaume Numérique
[cite_start]Ce rapport vous invite à un voyage. [cite_start]Nous n'allons pas simplement étudier un logiciel ; nous allons explorer un royaume numérique. [cite_start]Active Directory (AD) est ce royaume. [cite_start]Vous, futur administrateur, en êtes le gardien. [cite_start]Les utilisateurs sont les citoyens, les ordinateurs sont les habitations et les forteresses, les données sont les trésors et les secrets d'État. [cite_start]Les attaquants sont les espions, les assassins et les armées ennemies qui cherchent à s'emparer des "clés du royaume".
[cite_start]Active Directory n'est pas un simple annuaire ; c'est la pierre angulaire de l'infrastructure informatique pour 90 % des entreprises mondiales. [cite_start]Il centralise la gestion de l'identité et l'authentification des utilisateurs et des ressources. [cite_start]Si Active Directory ne fonctionne pas, l'entreprise s'arrête. [cite_start]Comprendre sa structure est important, mais maîtriser sa sécurité est une responsabilité absolue. [cite_start]Ce guide est conçu pour vous faire passer de simple visiteur à gardien expert de ce royaume vital, en décomposant sa complexité en briques compréhensibles et en mettant un accent constant sur l'art de sa défense.
Partie I : Cartographier le Royaume - Les Structures Fondamentales d'Active Directory
[cite_start]La manière dont un royaume est organisé, avec ses provinces, ses cités et ses frontières, détermine sa capacité à se défendre et à prospérer. [cite_start]Il en va de même pour Active Directory. [cite_start]La manière dont vous structurez votre environnement en domaines, arbres et forêts n'est pas une simple question de rangement. [cite_start]C'est la première et l'une des plus fondamentales décisions de sécurité que vous prendrez. [cite_start]Une forêt est la frontière de sécurité ultime. [cite_start]Un domaine est une frontière administrative et de réplication. [cite_start]Une conception mal avisée de cette structure peut introduire des failles de sécurité systémiques avant même la création du premier utilisateur. [cite_start]Chaque choix architectural a des implications profondes sur la manière dont les permissions sont gérées et dont les attaques peuvent être contenues ou, au contraire, se propager.
Chapitre 1 : Le Domaine - La Capitale du Royaume
Le Service d'Annuaire : La Grande Bibliothèque du Royaume
[cite_start]Au cœur de tout royaume se trouve un registre central de ses habitants, de ses terres et de ses lois. [cite_start]Active Directory est, avant tout, un service d'annuaire. [cite_start]Imaginez une bibliothèque gigantesque qui ne contient pas de livres, mais des fiches détaillées sur chaque "objet" du royaume : chaque citoyen (compte utilisateur), chaque chariot (compte ordinateur), chaque guilde (groupe), et même chaque imprimante. [cite_start]Cet annuaire centralisé permet aux administrateurs de gérer, d'organiser et de sécuriser toutes les ressources du réseau à partir d'un seul endroit, éliminant le chaos de la gestion individuelle de chaque machine.
Le Domaine : La Cité-État
[cite_start]Un domaine est un groupe logique d'objets (utilisateurs, ordinateurs, etc.) qui partagent la même base de données d'annuaire et sont soumis à un ensemble commun de règles. [cite_start]C'est la capitale de votre territoire, une cité-état administrative. [cite_start]Par exemple, tous les citoyens et ressources de Ventes.MonRoyaume.com appartiennent à ce domaine. [cite_start]Il définit une frontière administrative claire : les lois (stratégies) et les administrateurs de ce domaine gèrent tout ce qui s'y trouve, et leurs pouvoirs s'arrêtent aux frontières du domaine.
Le Contrôleur de Domaine (DC) : Le Château Fort
[cite_start]Si le domaine est la capitale, le Contrôleur de Domaine (DC) est son château fort. [cite_start]Le DC est un serveur Windows qui héberge et protège la "grande bibliothèque" (la base de données Active Directory, ntds.dit). [cite_start]C'est le cœur battant du domaine, et son rôle est double et critique:
 * [cite_start]Authentification : Il agit comme le garde à l'entrée du château. [cite_start]Quand un utilisateur tente de se connecter, c'est le DC qui vérifie son identité en comparant les informations fournies avec sa base de données.
 * [cite_start]Autorisation : Une fois l'utilisateur authentifié, le DC informe les autres services du réseau de ce que cet utilisateur a le droit de faire.
[cite_start]Pour garantir la résilience, il est crucial d'avoir au moins deux Contrôleurs de Domaine par domaine. [cite_start]Ces DC se répliquent constamment les informations entre eux grâce à un mécanisme de réplication multi-maître.
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
Un royaume peut s'étendre. [cite_start]Dans Active Directory, un arbre est une collection d'un ou plusieurs domaines qui partagent un espace de nom DNS contigu et hiérarchique.
 * [cite_start]Métaphore : Si MonRoyaume.com est le domaine parent, alors Ventes.MonRoyaume.com et RH.MonRoyaume.com sont des domaines enfants. [cite_start]Ils forment ensemble une seule dynastie, un seul "arbre". [cite_start]Une relation de confiance bidirectionnelle et transitive est automatiquement établie entre eux.
La Forêt : La Frontière Ultime de l'Empire
La forêt est le conteneur de plus haut niveau. [cite_start]C'est un ensemble d'un ou plusieurs arbres qui ne partagent pas nécessairement le même espace de nom.
 * [cite_start]Métaphore : Notre empire (MonRoyaume.com) peut former une alliance avec un autre royaume (RoyaumeAcquis.net). [cite_start]Les deux arbres coexistent alors dans la même "forêt".
 * [cite_start]Importance Critique : La forêt est la frontière de sécurité. Tous les domaines d'une même forêt partagent :
   * [cite_start]Un Schéma Commun : Le plan de construction qui définit les objets est identique pour toute la forêt.
   * [cite_start]Un Catalogue Global : Un index partiel contenant les informations sur chaque objet de la forêt pour des recherches rapides.
[cite_start]Une relation de confiance est automatiquement établie entre tous les domaines de la forêt[cite_start], ce qui signifie qu'un compte d'un domaine peut potentiellement accéder à une ressource dans un autre. [cite_start]La décision de créer une ou plusieurs forêts est donc une décision de sécurité majeure.
graph TD
    subgraph Forêt
        subgraph Arbre 1
            A(MonRoyaume.com - Racine) --> B(Ventes.MonRoyaume.com)
            A --> C(RH.MonRoyaume.com)
        end
        subgraph Arbre 2
            D(RoyaumeAcquis.net - Racine) --> E(Prod.RoyaumeAcquis.net)
        end
        A <-->|Relation de confiance<br>transitive| D
    end

Partie II : Les Habitants et les Lois du Royaume - Objets et Politiques
[cite_start]La manière dont les objets sont gérés et les politiques (GPO) sont appliquées définit directement la surface d'attaque. [cite_start]Une structure d'Unités d'Organisation (OU) désorganisée ou un abus des groupes ne sont pas de simples problèmes de propreté ; ce sont des vulnérabilités actives.
Chapitre 3 : Les Habitants du Royaume - Objets AD
[cite_start]L'annuaire est peuplé d'objets représentant les ressources du réseau.
 * [cite_start]Objets Feuilles : Objets finaux comme les utilisateurs, ordinateurs, ou imprimantes.
 * [cite_start]Objets Conteneurs : Objets pouvant en contenir d'autres, comme les Domaines, les Groupes et les Unités d'Organisation (OU).
L'Unité d'Organisation (OU) : Les Ministères et Provinces
[cite_start]L'OU est le conteneur le plus flexible pour l'administration.
 * [cite_start]Métaphore : Pensez aux OU comme aux ministères (Ventes, Finance) ou aux provinces (Paris, Lyon) de votre royaume.
 * [cite_start]Rôle Fondamental : Contrairement aux conteneurs par défaut, les OU sont essentielles pour deux tâches:
   * [cite_start]Lier des Stratégies de Groupe (GPO) pour appliquer des configurations.
   * [cite_start]Déléguer des droits administratifs de manière limitée.
Les Groupes : Les Guildes et Confréries
[cite_start]Les groupes sont des collections d'objets utilisés pour gérer les permissions.
 * Groupes de Sécurité :
   * [cite_start]Métaphore : Ce sont les détenteurs de clés du royaume. [cite_start]Vous donnez une clé au "Groupe de la Garde Royale" plutôt qu'à chaque membre individuellement.
   * [cite_start]Fonction : Assigner des permissions d'accès à des ressources. [cite_start]Ils possèdent un identifiant de sécurité (SID) et peuvent être ajoutés aux Listes de Contrôle d'Accès (ACL).
 * Groupes de Distribution :
   * [cite_start]Métaphore : Des listes de diffusion pour les messagers du roi.
   * [cite_start]Fonction : Créer des listes d'adresses e-mail. [cite_start]Ils n'ont pas de SID et ne peuvent PAS être utilisés pour accorder des permissions.
| Caractéristique | Groupe de Sécurité | Groupe de Distribution |
|---|---|---|
| Objectif Principal | [cite_start]Gérer les permissions et les accès aux ressources | [cite_start]Créer des listes d'e-mails pour la communication |
| Peut recevoir des permissions? | [cite_start]Oui (possède un SID, peut être dans une ACL) | [cite_start]Non (n'est pas un principal de sécurité) |
| Peut être utilisé pour envoyer des e-mails? | [cite_start]Oui | [cite_start]Oui |
| Cas d'usage typique | [cite_start]Donner l'accès au dossier "Comptabilité" | [cite_start]Envoyer une newsletter à toute l'entreprise |
| Considération de performance | [cite_start]Un trop grand nombre d'appartenances peut ralentir la connexion | [cite_start]Aucun impact sur la performance de connexion |
Chapitre 4 : La Délégation de Contrôle - Nommer des Gouverneurs Locaux
[cite_start]La délégation de contrôle permet de confier des tâches administratives spécifiques sur des périmètres limités (généralement des OU) à d'autres utilisateurs.
 * [cite_start]Métaphore : Le roi nomme un gouverneur pour la province de "Lyon". [cite_start]Ce gouverneur (le groupe "Support Technique Lyon") peut gérer les utilisateurs de sa province uniquement.
 * [cite_start]Focus Sécurité : La délégation est un outil puissant pour appliquer le principe du moindre privilège, mais une mauvaise configuration peut créer des failles de sécurité béantes.
<!-- end list -->
graph TD
    A(Admin du Domaine) -- "Délègue la tâche via l'Assistant" --> B(OU 'Ventes')
    subgraph B
        C[Utilisateur 'Jean Vente']
        D[Ordinateur 'PC-Jean']
    end
    F(Support Technique) -- "Reçoit la permission sur OU 'Ventes'" --> B
    F -- "Peut maintenant réinitialiser le mot de passe de" --> C
    F -.->|N'a aucun droit sur| G(OU 'Finance')

Chapitre 5 : Les Stratégies de Groupe (GPO) - Le Code de Lois du Royaume
[cite_start]Les GPO sont le principal mécanisme pour définir et imposer des règles de manière centralisée.
 * [cite_start]Métaphore : Les GPO sont le code de lois du royaume. [cite_start]Une loi peut imposer une longueur de mot de passe pour tous (GPO liée au domaine) ou un fond d'écran spécifique pour un département (GPO liée à une OU).
 * [cite_start]Ordre d'Application (LSDOU) : Les politiques s'appliquent dans un ordre strict [cite_start]: Local -> Site -> Domaine -> OU.
 * [cite_start]La Règle d'Or : La politique la plus proche de l'objet l'emporte.
Partie III : La Garde Royale - Authentification et Contrôle d'Accès
[cite_start]La sécurité repose sur l'authentification (savoir qui entre) et l'autorisation (contrôler ce qu'ils peuvent faire). [cite_start]Kerberos prouve qui vous êtes, les ACL décident ce que vous pouvez faire.
Chapitre 6 : Kerberos - Le Protocole des Sceaux et des Laissez-passer
[cite_start]Kerberos est le protocole d'authentification par défaut dans AD, conçu pour être robuste sur des réseaux non-sûrs.
 * [cite_start]Les Acteurs : Le Client, le Service et le KDC (Key Distribution Center), qui se trouve sur le Contrôleur de Domaine.
 * Flux d'Authentification :
   * [cite_start]AS-REQ / AS-REP : Le client prouve son identité à l'Authentication Service (AS) du KDC et reçoit un Ticket-Granting Ticket (TGT), un laissez-passer général.
   * [cite_start]TGS-REQ / TGS-REP : Le client présente son TGT au Ticket Granting Service (TGS) pour demander un Ticket de Service (ST) pour une ressource spécifique.
   * [cite_start]AP-REQ / AP-REP : Le client présente le ST au service, qui le valide et accorde l'accès.
<!-- end list -->
sequenceDiagram
    participant Client
    participant KDC_AS as KDC (Guichet AS)
    participant KDC_TGS as KDC (Guichet TGS)
    participant Service

    Client->>KDC_AS: 1. AS-REQ: Je suis Bob, je veux un TGT
    KDC_AS-->>Client: 2. AS-REP: Voici ton TGT (chiffré avec secret KDC)
    Client->>KDC_TGS: 3. TGS-REQ: Voici mon TGT, je veux un ticket pour le Service X
    KDC_TGS-->>Client: 4. TGS-REP: Voici ton Ticket de Service pour X (chiffré avec secret de X)
    Client->>Service: 5. AP-REQ: Voici mon Ticket de Service pour toi
    Service-->>Client: 6. AP-REP (optionnel): Accès accordé/refusé

Chapitre 7 : Les Listes de Contrôle d'Accès (ACL) - Qui a la Clé de Quelle Porte?
[cite_start]Une fois authentifié, les ACL déterminent ce qu'un utilisateur peut faire. [cite_start]Une ACL est une liste de permissions attachée à un objet. [cite_start]Chaque permission est une Entrée de Contrôle d'Accès (ACE) qui spécifie un principal de sécurité, une permission et un type (Allow/Deny). [cite_start]Une règle de refus (Deny) l'emporte toujours sur une autorisation (Allow).
Partie IV : L'Art de la Guerre - Sécuriser le Royaume Contre les Menaces
[cite_start]La compromission d'Active Directory est une chaîne d'attaque méthodique : compromission initiale, reconnaissance, mouvement latéral, escalade de privilèges et persistance.
Chapitre 9 : Les Attaques Courantes Décortiquées
 * [cite_start]Pass-the-Hash (PtH) : Un attaquant utilise le hash NTLM d'un mot de passe pour s'authentifier à la place d'un utilisateur, sans avoir besoin de connaître le mot de passe en clair.
 * Kerberoasting : Un attaquant demande un Ticket de Service pour un service utilisant un compte avec un mot de passe faible. [cite_start]Il récupère le ticket, qui est chiffré avec le hash du mot de passe du service, et le craque hors ligne.
 * Golden & Silver Tickets :
   * Golden Ticket : L'attaquant a volé le hash du compte krbtgt. Il peut alors forger un TGT (laissez-passer général) pour n'importe qui, avec n'importe quels privilèges. [cite_start]C'est le "game over" pour la sécurité du domaine.
   * Silver Ticket : L'attaquant a volé le hash d'un compte de service. [cite_start]Il peut forger un Ticket de Service (ST) pour accéder à ce service spécifique de manière très discrète.
| Caractéristique | Golden Ticket Attack | Silver Ticket Attack |
|---|---|---|
| Ticket Falsifié | [cite_start]TGT (Ticket-Granting Ticket) | [cite_start]TGS/ST (Ticket-Granting Service) |
| Secret Requis | [cite_start]Hash NTLM du compte KRBTGT | [cite_start]Hash NTLM du compte de service cible |
| Portée de l'Accès | [cite_start]Toute la forêt/domaine (illimité) | [cite_start]Uniquement le service spécifique sur un serveur |
| Interaction avec le DC | [cite_start]L'attaquant demande des TGS légitimes au DC | [cite_start]Aucune interaction avec le DC une fois le ticket forgé |
| Difficulté de Détection | [cite_start]Difficile, mais génère du trafic vers le DC | [cite_start]Très difficile, pas de trafic anormal vers le DC |
Chapitre 10 : La Stratégie de Défense en Profondeur
 * [cite_start]Le Principe du Moindre Privilège (PoLP) : Aucun utilisateur ou système ne doit avoir plus de droits que ce qui est strictement nécessaire.
 * [cite_start]Le Modèle de Tiers (Tier Model) : Divise l'infrastructure en niveaux pour contenir les attaques.
   * [cite_start]Tier 0 : Le cœur du royaume (Contrôleurs de Domaine, comptes à hauts privilèges).
   * [cite_start]Tier 1 : Les infrastructures clés (serveurs d'entreprise, bases de données).
   * [cite_start]Tier 2 : Les postes de travail et comptes utilisateurs standards.
   * [cite_start]Règle Fondamentale : Un niveau inférieur ne peut JAMAIS administrer un niveau supérieur.
<!-- end list -->
graph TD
    subgraph Tier 0 [Tier 0 - Cœur du Royaume]
        A(Contrôleurs de Domaine)
        B(Comptes Admins Dom/Ent)
    end
    subgraph Tier 1 [Tier 1 - Serveurs]
        C(Serveurs Applicatifs)
        D(Admins Serveurs)
    end
    subgraph Tier 2 [Tier 2 - Postes de Travail]
        E(Postes Utilisateurs)
        F(Comptes Utilisateurs)
    end

    F -- "Utilise" --> E
    D -- "Administre" --> C
    F -- "Ne peut PAS administrer" --x C
    D -- "Ne peut PAS administrer" --x A

    style A fill:#ff9999,stroke:#333,stroke-width:2px
    style B fill:#ff9999,stroke:#333,stroke-width:2px
    style C fill:#ffcc99,stroke:#333,stroke-width:2px
    style D fill:#ffcc99,stroke:#333,stroke-width:2px
    style E fill:#99ccff,stroke:#333,stroke-width:2px
    style F fill:#99ccff,stroke:#333,stroke-width:2px

 * Sécuriser les Contrôleurs de Domaine : Leur protection est non négociable. [cite_start]Pas d'accès Internet, correctifs rapides, pare-feu, et désactivation des services non essentiels.
Conclusion : Le Devoir du Gardien
[cite_start]La sécurité d'Active Directory n'est pas un projet avec une fin, mais un processus continu de vigilance, d'audit et d'amélioration. [cite_start]Les menaces évoluent, votre défense doit en faire de même. [cite_start]La théorie est posée, la prochaine étape est la pratique. [cite_start]Montez un laboratoire, testez, auditez et simulez des attaques. [cite_start]Le gardien ne dort jamais ; il veille sur son royaume. [cite_start]Votre veille commence maintenant.
