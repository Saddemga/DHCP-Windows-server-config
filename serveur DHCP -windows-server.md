# Configuration d'un serveur DHCP sur Windows Server dans une VM



## Étape 1 : Configurer le réseau interne



**Configurer une adresse IP statique pour le serveur :**
   - Dans Windows Server, ouvrez les paramètres réseau.
   - Assignez une adresse IP statique sur le réseau `172.20.0.0/24`.
     - Adresse IP : `172.20.0.1`
     - Masque de sous-réseau : `255.255.255.0`
     - Passerelle : Laissez vide (pas nécessaire en réseau interne).

---

## Étape 2 : Configurer le serveur DHCP

### 2.1 Renommer le serveur

1. Ouvrez **Gestionnaire de serveur**.
2. Cliquez sur **Local Server** > **Computer Name**.
3. Modifiez le nom d'hôte en `SRV-DHCP`.
4. Redémarrez le serveur pour appliquer les modifications.

### 2.2 Installer le rôle DHCP

1. Dans **Gestionnaire de serveur**, cliquez sur **Ajouter des rôles et fonctionnalités**.
2. Sélectionnez **Installation basée sur un rôle ou une fonctionnalité**.
3. Choisissez le serveur actuel (`SRV-DHCP`).
4. Cochez **DHCP Server** et complétez l'installation.
5. Une fois terminé, ouvrez la console **DHCP** (via le menu Démarrer).

### 2.3 Configurer le service DHCP

1. **Créer une nouvelle plage d'adresses IP :**
   - Dans la console DHCP, faites un clic droit sur **IPv4** > **Nouvelle plage**.
   - Nom : `Plage_172.20`
   - Plage d'adresses :
     - Début : `172.20.0.100`
     - Fin : `172.20.0.200`
   - Masque de sous-réseau : `255.255.255.0`.
   - Exclure les adresses (optionnel) : `172.20.0.1` (IP du serveur).
   - Durée du bail : Par défaut (8 jours).

2. **Activer la plage d'adresses :**
   - Terminez l'assistant et assurez-vous que la plage est activée.

3. **Configurer une réservation statique :**
   - Cliquez avec le bouton droit sur **Réservations** > **Nouvelle réservation**.
   - Nom : `Machine-Client1`
   - Adresse IP : `172.20.0.10`
   - Adresse MAC : (trouver via `ipconfig /all` sur le client).
   - Description : `Réservation pour Client1`.

4. **Démarrer le service DHCP :**
   - Vérifiez que le service DHCP est actif via la console **Services**.

---

## Étape 3 : Tester la configuration

### 3.1 Vérifier l'attribution IP dynamique

1. Configurez une machine cliente sur le même réseau interne.
2. Assurez-vous que la carte réseau du client est en **DHCP automatique**.
3. Redémarrez le client ou exécutez `ipconfig /renew`.
4. Vérifiez que l'IP assignée est dans la plage `172.20.0.100 - 172.20.0.200` avec `ipconfig`.

### 3.2 Vérifier l'attribution statique

1. Configurez une autre machine cliente avec l'adresse MAC définie dans la réservation.
2. Assurez-vous que la carte réseau est en **DHCP automatique**.
3. Redémarrez la machine ou exécutez `ipconfig /renew`.
4. Vérifiez que l'adresse IP assignée est `172.20.0.10`.

### 3.3 Valider les critères

- Sur le serveur, ouvrez **Gestionnaire DHCP** et assurez-vous que les baux dynamiques et réservés apparaissent correctement dans la section "Baux actifs".
- Modifiez l'adresse IP du client avec réservation pour qu'elle ne corresponde plus. Exécutez `ipconfig /renew` et confirmez que le client reçoit toujours `172.20.0.10`.

---

##  Conclusion

Votre serveur DHCP est maintenant configuré et opérationnel. Vous avez mis en place une plage d'adresses dynamiques, une réservation statique, et testé la configuration avec succès.
