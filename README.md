# 🌐 Lab OSPF Multi-Area — Entreprise Multi-Sites

Lab de routage OSPF multi-area réalisé dans **Cisco Packet Tracer**, simulant une entreprise avec un siège (HQ) et un site distant (SITE2), incluant le routage inter-VLAN et la redistribution de route par défaut.

## Topologie

```
                        [ISP]
                    209.165.200.1/30
                         |
                         | .2
                      [R-EDGE]              ← Area 0 + Default Route
                     /         \
                .1 /             \ .1
               /                   \
         10.0.12.0/30          10.0.13.0/30
             /                       \
         .2 /                         \ .2
        [R-HQ]                      [R-SITE2]       ← Area 2
      Area 0 + Area 1                  |
          |                        192.168.20.0/24
     [SW-HQ] (L2)                      |
      |          |                   [PC3]
   VLAN 10    VLAN 20
 192.168.     192.168.
  10.0/24      11.0/24
    |             |
  [PC1]         [PC2]
```

## Plan d'adressage

### Liens inter-routeurs (Area 0)

| Lien              | Réseau         | Interface R1       | IP R1      | Interface R2       | IP R2      |
|-------------------|----------------|--------------------|------------|--------------------|------------|
| R-EDGE ↔ ISP     | 209.165.200.0/30 | Gig0/0           | .2         | Gig0/0             | .1         |
| R-EDGE ↔ R-HQ    | 10.0.12.0/30   | Gig0/1             | .1         | Gig0/0             | .2         |
| R-EDGE ↔ R-SITE2 | 10.0.13.0/30   | Gig0/2             | .1         | Gig0/0             | .2         |

### Réseaux LAN

| Site   | VLAN | Réseau            | Gateway        | Area   |
|--------|------|-------------------|----------------|--------|
| HQ     | 10   | 192.168.10.0/24   | 192.168.10.1   | Area 1 |
| HQ     | 20   | 192.168.11.0/24   | 192.168.11.1   | Area 1 |
| SITE2  | —    | 192.168.20.0/24   | 192.168.20.1   | Area 2 |

## Technologies utilisées

- **OSPF Multi-Area** — Area 0 (backbone), Area 1 (HQ LANs), Area 2 (SITE2 LAN)
- **VLANs** — VLAN 10 (Bureaux) et VLAN 20 (Direction) sur SW-HQ
- **Router-on-a-stick** — Routage inter-VLAN via sous-interfaces sur R-HQ
- **Redistribution de route par défaut** — `default-information originate` sur R-EDGE

## Structure du repo

```
ospf-multiarea-lab/
├── README.md
├── configs/
│   ├── R-EDGE.txt
│   ├── R-HQ.txt
│   ├── R-SITE2.txt
│   ├── ISP.txt
│   └── SW-HQ.txt
└── topologie.pkt
```

## Concepts clés

### ABR (Area Border Router)

- **R-HQ** est ABR entre Area 0 et Area 1
- **R-SITE2** est ABR entre Area 0 et Area 2
- Les ABR génèrent des LSA Type 3 pour partager les routes entre areas

### Types de routes OSPF visibles

| Code     | Signification               | Exemple                        |
|----------|------------------------------|--------------------------------|
| `O`      | Route intra-area             | Réseau dans la même area       |
| `O IA`   | Route inter-area             | Réseau d'une autre area        |
| `O*E2`   | Route externe par défaut     | 0.0.0.0/0 via R-EDGE           |

### Types de LSA

| Type | Nom            | Généré par | Portée          |
|------|----------------|------------|-----------------|
| 1    | Router LSA     | Tous       | Intra-area      |
| 2    | Network LSA    | DR         | Intra-area      |
| 3    | Summary LSA    | ABR        | Inter-area      |
| 5    | External LSA   | ASBR       | Tout le domaine |

## Commandes de vérification

```bash
# Vérifier les adjacences OSPF
show ip ospf neighbor

# Voir la base de données OSPF (LSA)
show ip ospf database

# Voir les routes apprises par OSPF
show ip route ospf

# Vérifier les interfaces OSPF
show ip ospf interface brief
```

## Résultats attendus

- ✅ PC1 (VLAN 10) ↔ PC2 (VLAN 20) — routage inter-VLAN
- ✅ PC1 ↔ PC3 — routage inter-area (Area 1 → Area 0 → Area 2)

## Outils

- **Cisco Packet Tracer** 8.x
- Niveau : Intermédiaire (préparation CCNA)

## Auteur

Lab réalisé dans le cadre de la préparation au CCNA.
# OSPF-MULTI-AREA-LAB
