---
layout: post
title: "Windows LAPS avec Entra ID : configuration complète"
description: "Gérer le compte admin local en full cloud, sans on-premise. Guide pas à pas avec les pièges à éviter."
date: 2025-01-15
section: it
tags: [m365, activedirectory, intune]
---

Windows LAPS (Local Administrator Password Solution) existe depuis longtemps dans sa version on-premise. Depuis 2023, Microsoft a sorti une version native intégrée à Entra ID — plus besoin d'AD on-prem pour gérer les mots de passe admin locaux.

Voici comment ça se configure proprement.

## Prérequis

- Tenant M365 avec licence Entra ID P1 minimum
- Machines jointes à Entra ID (Entra Join) ou hybrides
- Windows 11 22H2 / Windows 10 22H2 ou supérieur

> Les machines en Hybrid Join fonctionnent aussi, mais la rotation du mot de passe passe par Entra, pas par le DC on-prem.

## Activer LAPS dans Entra ID

Dans le portail Entra ID > **Devices > Device settings**, activer **Enable Microsoft Entra Local Administrator Password Solution (LAPS)**.

C'est tout pour la partie cloud.

## Créer la policy Intune

Dans le portail Intune > **Endpoint security > Account protection > Create policy** :

- Platform : Windows 10 and later
- Profile : Local admin password solution (Windows LAPS)

Paramètres importants :

```
Backup directory : Azure Active Directory
Password age days : 30
Administrator account name : (laisser vide = compte Administrator par défaut)
Password complexity : Large letters + small letters + numbers + special chars
Password length : 16
```

Si tu veux utiliser un compte admin custom (recommandé) :

```
Administrator account name : rdadmin
```

Dans ce cas, le compte `rdadmin` doit exister sur les machines. Tu peux le créer via un script PowerShell déployé par Intune ou via une autre policy.

## Récupérer un mot de passe

Via le portail Entra ID > **Devices > [nom du device] > Local administrator password**.

Via PowerShell :

```powershell
# Module requis
Install-Module Microsoft.Graph -Scope CurrentUser

Connect-MgGraph -Scopes "DeviceLocalCredential.Read.All"

Get-MgDeviceLocalCredential -DeviceId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## Déléguer l'accès

Pour ne pas donner accès à tout le monde, créer un rôle custom dans Entra ID avec uniquement la permission `microsoft.directory/deviceLocalCredentials/password/read`.

Assigner ce rôle aux techniciens niveau 1/2 qui ont besoin d'accès admin local ponctuel.

## Piège classique

Si tu as déjà **Device Encryption** activé automatiquement sur tes machines, et que tu déploies LAPS avec un compte custom, vérifie que le compte custom est bien créé **avant** que LAPS essaie de faire sa première rotation. Sinon la policy échoue en erreur silencieuse et le mot de passe n'est jamais sauvegardé dans Entra.

Le bon ordre : script de création du compte → policy LAPS → vérification dans les device logs Intune.
