<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Welcome file</title>
  <link rel="stylesheet" href="https://stackedit.io/style.css" />
</head>

<body class="stackedit">
  <div class="stackedit__html"><h1 id="next-lb--architecture-standardisée-pour-projets-next.js">Next-LB : Architecture Standardisée pour Projets Next.js</h1>
<p>Cette documentation contient la base d’un projet Next.js structuré selon une architecture propre, visant à garantir la cohérence du code, la maintenabilité et la scalabilité. Ce document sert de guide pour tous les développeurs travaillant sur des projets Next.js.</p>
<h2 id="section"><img src="https://i.pinimg.com/736x/5e/f2/ee/5ef2ee333b101ee58b5f0236be633d3d.jpg" alt="enter image description here"></h2>
<h2 id="principes-darchitecture-adoptés">1. Principes d’Architecture Adoptés</h2>
<p>Nous combinons plusieurs patrons d’architecture modernes, particulièrement adaptés à l’environnement Next.js / React, pour optimiser la structure de nos projets :</p>
<ul>
<li>
<p><strong>Tranches Verticales (Vertical Slices / Feature Slices)</strong> : L’organisation du code est basée sur les <strong>fonctionnalités métier (features)</strong> plutôt que sur des couches techniques horizontales. Chaque fonctionnalité est autonome et regroupe tous les éléments la concernant. Cela favorise la modularité et la clarté.</p>
</li>
<li>
<p><strong>MVVM (Model-View-ViewModel)</strong> : Ce patron est implémenté naturellement dans nos applications React/Next.js :</p>
<ul>
<li>
<p>Le <strong>Modèle</strong> : Représenté par les définitions de données (types, schemas), les interfaces avec le backend (api) et les logiques serveur encapsulées (actions).</p>
</li>
<li>
<p>La <strong>Vue</strong> : Correspond aux composants React (<strong>components</strong>) et aux pages (<code>app/[locale]/[module]</code>) qui affichent l’interface utilisateur.</p>
</li>
<li>
<p>Le <strong>ViewModel</strong> : Géré par les <strong>hooks</strong> personnalisés (hooks) et les hooks de gestion de données (<strong>queries</strong> via TanStack Query). Il contient la logique de présentation et d’état de l’interface utilisateur, agissant comme un intermédiaire entre le Modèle et la Vue.</p>
</li>
</ul>
</li>
<li>
<p><strong>Principes de Clean Architecture / Hexagonal</strong> : Pour les logiques métier complexes, nous appliquons ces principes pour isoler le “cœur” de l’application (règles métier, entités) des détails d’infrastructure (UI, base de données, API). Cela garantit une meilleure testabilité et une indépendance vis-à-vis des frameworks.</p>
</li>
</ul>
<hr>
<h2 id="structure-des-dossiers-principaux">2. Structure des Dossiers Principaux</h2>
<p>Voici la structure de haut niveau du projet. Elle est conçue pour être intuitive et prévisible.</p>
<pre><code>start/
├── app/                  # App Router Next.js 14 : pages, layouts, groupes de routes
│   └── [locale]/         # Support de l'internationalisation (ex: en, ar)
│       └── (protected)/  # Routes protégées (authentification requise)
│           └── users/    # Exemple de page pour le module "utilisateur"
│               └── page.tsx
│       └── layout.tsx    # Layouts racines ou de groupes de routes
│
├── components/           # Composants React génériques et réutilisables dans toute l'application
│   ├── ui/               # Composants UI de base (shadcn/ui, Button, Input, Select)
│   ├── partials/         # Composants de layout (Header, Sidebar, Footer)
│   └── blocks/           # Blocs de contenu réutilisables (ex: StatusBlock)
│
├── features/             # **LE CŒUR** : Toutes les fonctionnalités métier, organisées en tranches verticales
│   └── [module_name]/    # Chaque dossier représente une fonctionnalité ou un domaine (ex: products, users, auth)
│       ├── actions/      # Actions serveur (Server Actions)
│       ├── api/          # Fonctions API brutes
│       ├── components/   # Composants spécifiques à ce module
│       ├── filters/      # Définitions des filtres pour Nuqs
│       ├── hooks/        # Hooks personnalisés (le ViewModel du module)
│       ├── queries/      # Hooks TanStack Query
│       ├── schemas/      # Schémas de validation Zod et types DTO
│       ├── tests/        # Tests unitaires et d'intégration
│       ├── types/        # Types TypeScript spécifiques au module
│       └── utils/        # Fonctions utilitaires / helpers spécifiques au module
│   ├── auth/             # Exemple de module d'authentification
│   └── notification/     # Exemple de module de notifications
│   └── utilisateur/      # **Exemple détaillé dans ce README**
│
├── hooks/                # Hooks React personnalisés globaux et réutilisables
├── i18n/                 # Fichiers de traduction pour `next-intl`
├── lib/                  # Utilitaires globaux, configurations, clients API centralisés
│   ├── api.ts            # Configuration Axios, intercepteurs centralisés
│   ├── get-query-client.ts # Configuration du `QueryClient` de TanStack Query
│   └── utils/            # Fonctions utilitaires globales
│
├── middlewares/          # Middlewares Next.js (ex: pour l'authentification, la gestion des locales)
├── providers/            # Providers React (thème, auth, etc.)
├── public/               # Assets statiques
├── styles/               # Styles globaux et spécifiques aux modules
│   └── [module]/         # Styles spécifiques à un module (ex: `products.module.css`)
│   └── globals.css       # Styles généraux (Tailwind, CSS de base)
│
└── types/                # Types TypeScript globaux (interfaces, énumérations partagées)

</code></pre>
<hr>
<h2 id="conventions-de-code-et-responsabilités-détaillées">3. Conventions de Code et Responsabilités Détaillées</h2>
<p>Pour maintenir la cohérence et l’efficacité, veuillez respecter scrupuleusement les conventions suivantes.</p>
<h3 id="dossier-applocalemodule-pages">3.1 Dossier app/[locale]/[module]/ (Pages)</h3>
<ul>
<li>
<p><strong>Responsabilité</strong> : Ce dossier contient les pages Next.js et leurs layouts. Les pages doivent être des <strong>Server Components</strong> autant que possible, avec un préchargement de données optimisé via TanStack Query.</p>
</li>
<li>
<p><strong>Contenu</strong> : Principalement des <code>page.tsx</code> et <code>layout.tsx</code>.</p>
</li>
<li>
<p><strong>À ne pas faire</strong> : Évitez d’y placer de la logique métier complexe ou des composants réutilisables non liés au routage. La logique doit être déléguée aux <code>features/</code> ou <code>components/</code> partagés.</p>
</li>
</ul>
<h3 id="dossier-featuresmodule_name-tranches-verticales">3.2 Dossier features/[module_name]/ (Tranches Verticales)</h3>
<p>Chaque module est une unité autonome et contient tout ce dont il a besoin.</p>
<h4 id="actions-server-actions-"><code>actions/</code> (Server Actions) :</h4>
<ul>
<li>
<p><strong>Fichiers</strong> : <code>[actionName].action.ts</code> (ex: <code>createProduct.action.ts</code>, <code>utilisateur.action.ts</code>)</p>
</li>
<li>
<p><strong>Contenu</strong> : Fonctions de <strong>Server Actions Next.js</strong> (“use server”). Elles encapsulent les appels aux fonctions de <code>features/[module]/api/</code> et sont destinées à être appelées depuis le côté client (form submit, etc.) ou serveur.</p>
</li>
<li>
<p><strong>Objectif</strong> : Protéger les informations sensibles du backend en ne les exposant pas côté client et centraliser la logique de mutation serveur.</p>
</li>
<li>
<p><strong>Convention</strong> : Elles doivent appeler les fonctions API de <code>features/[module]/api/</code>.</p>
</li>
<li>
<p><strong>À ne pas faire</strong> : <strong>NE JAMAIS</strong> appeler directement les API routes (de <code>features/[module]/api/</code>) dans les <code>queries/</code> côté client. Utilisez toujours les Server Actions pour cela.</p>
</li>
</ul>
<p>TypeScript</p>
<pre><code>// src/features/utilisateur/actions/utilisateur.action.ts (Extrait)
"use server"
import { utilisateurAPI } from "../api/utilisateur.api";
import { UtilisateurAddDTO, UtilisateurAddSchema } from "../schema/utilisateur.schema";
import { processAndValidateFormData } from "ak-zod-form-kit"; // Utilitaire de validation basé sur Zod

export const obtenirTousUtilisateursAction = (params: IUtilisateursParams) =&gt; {
    return utilisateurAPI.obtenirTousUtilisateurs(params);
}

export const ajouterUtilisateurAction = async (data: UtilisateurAddDTO) =&gt; {
    const result = processAndValidateFormData(UtilisateurAddSchema, data, { outputFormat: "object" });
    if (!result.success) {
        throw new Error(result.errorsInString || "Une erreur est survenue lors de la validation des données.");
    }
    return await utilisateurAPI.ajouterUtilisateur(data);
}
// ... autres actions (modifier, supprimer, activer/désactiver)

</code></pre>
<h4 id="api-api-routes-brutes-"><code>api/</code> (API Routes Brutes) :</h4>
<ul>
<li>
<p><strong>Fichiers</strong> : <code>[moduleName].api.ts</code> (ex: <code>products.api.ts</code>, <code>utilisateur.api.ts</code>)</p>
</li>
<li>
<p><strong>Contenu</strong> : Fonctions asynchrones qui encapsulent les appels HTTP bruts via le client <code>api</code> configuré dans <code>lib/api.ts</code>.</p>
</li>
<li>
<p><strong>Objectif</strong> : Représenter l’interface directe avec le backend. Elles sont la première couche d’interaction avec l’API externe.</p>
</li>
<li>
<p><strong>Gestion d’erreur</strong> : En cas d’échec (statut HTTP non 2xx), une exception doit être levée automatiquement par la configuration de <code>lib/api.ts</code> pour une gestion centralisée.</p>
</li>
<li>
<p>Elles ne connaissent <strong>PAS</strong> TanStack Query ni les Server Actions.</p>
</li>
</ul>
<p>TypeScript</p>
<pre><code>// src/features/utilisateur/api/utilisateur.api.ts (Extrait)
import { api } from "@/lib/api"; // Client HTTP centralisé (basé sur axios)
import { IUtilisateur, IUtilisateursParams, IUtilisateurAddUpdateResponse } from "../types/utilisateur.type";
import { PaginatedResponse } from "@/types";
import { UtilisateurAddDTO } from "../schema/utilisateur.schema";

export const utilisateurAPI = {
    obtenirTousUtilisateurs(params: IUtilisateursParams): Promise&lt;PaginatedResponse&lt;IUtilisateur&gt;&gt; {
        return api.request&lt;PaginatedResponse&lt;IUtilisateur&gt;&gt;({
            endpoint: `/users`,
            method: "GET",
            searchParams: params,
        });
    },
    ajouterUtilisateur(data: UtilisateurAddDTO): Promise&lt;IUtilisateurAddUpdateResponse&gt; {
        return api.request&lt;IUtilisateurAddUpdateResponse&gt;({
            endpoint: `/users`,
            method: "POST",
            data,
        });
    },
    // ... autres fonctions API (obtenir un seul, modifier, supprimer, stats)
};

</code></pre>
<h4 id="components-"><code>components/</code> :</h4>
<ul>
<li>
<p><strong>Contenu</strong> : Composants React qui sont spécifiques à cette fonctionnalité et ne seront pas réutilisés ailleurs. Si un composant est partagé entre plusieurs modules, il doit aller dans <code>components/</code> à la racine.</p>
</li>
<li>
<p><strong>Convention de nommage</strong> : <code>[nom-du-module]-[traitement]-[type-de-composant].tsx</code>.</p>
<ul>
<li>
<p><code>[nom-du-module]</code> : Le nom du module (ex: <code>utilisateur</code>).</p>
</li>
<li>
<p><code>[traitement]</code> : L’action ou la vue principale (ex: <code>add</code>, <code>edit</code>, <code>delete</code>, <code>view</code>).</p>
</li>
<li>
<p><code>[type-de-composant]</code> : Le type de composant (ex: <code>form</code>, <code>modal</code>, <code>table</code>, <code>list</code>).</p>
</li>
</ul>
</li>
<li>
<p><strong>Exemples</strong> : <code>utilisateur-add-form.tsx</code>, <code>utilisateur-edit-modal.tsx</code>, <code>utilisateur-view-table.tsx</code>, <code>utilisateur-view-list.tsx</code>.</p>
</li>
<li>
<p><strong>Nommage fichier/dossier</strong> : En minuscule.</p>
</li>
<li>
<p><strong>Nommage fonction/composant</strong> : En <code>PascalCase</code> (ex: <code>UtilisateurAddForm</code>).</p>
</li>
<li>
<p><strong>Gestion des données</strong> : Un composant doit gérer ses données, ses requêtes (via les hooks de <code>queries/</code> ou <code>actions/</code>) et ses affichages.</p>
</li>
</ul>
<p>TypeScript</p>
<pre><code>// src/features/utilisateur/components/utilisateur-list/index.tsx (Extrait)
// Ce composant est un exemple de "Vue" qui utilise un "ViewModel" (useUtilisateurListTable)
// et d'autres composants spécifiques au module.
"use client";
import { useUtilisateurListTable } from "../../hooks/useUtilisateurListTable";
import { UtilisateurAddModal } from "../utilisateur-modal/utilisateur-add-modal";
// ... autres imports de composants UI et modales

export function UserList({ type }: { type: "personnel" | "demandeur" }) {
    const columns = getColumns({ type });
    const { table, isLoading, isError, error, isFetching, modalStates, modalHandlers, currentUser, filters } =
        useUtilisateurListTable({ columns, type });

    return (
        &lt;div className="w-full"&gt;
            {/* ... Options de filtrage et barre d'outils ... */}
            &lt;Table&gt;
                {/* ... En-têtes et corps de la table avec flexRender ... */}
            &lt;/Table&gt;
            &lt;TablePagination table={table} /&gt;

            {/* Modales */}
            &lt;UtilisateurAddModal isOpen={modalStates.addOpen} setIsOpen={modalHandlers.setAddOpen} /&gt;
            {currentUser &amp;&amp; (
                &lt;&gt;
                    &lt;UtilisateurUpdateModal /* ... */ /&gt;
                    &lt;UtilisateurDeleteModal /* ... */ /&gt;
                    &lt;UtilisateurLockUnlockModal /* ... */ /&gt;
                &lt;/&gt;
            )}
        &lt;/div&gt;
    );
}

</code></pre>
<h4 id="filters-"><code>filters/</code> :</h4>
<ul>
<li>
<p><strong>Fichiers</strong> : <code>[moduleName].filters.ts</code> (ex: <code>utilisateur.filters.ts</code>)</p>
</li>
<li>
<p><strong>Contenu</strong> : Définitions des schémas de parsing pour les paramètres de requête d’URL, utilisés pour filtrer et paginer les listes. Utilise <a href="https://www.npmjs.com/package/nuqs">nuqs</a> pour la synchronisation avec l’URL.</p>
</li>
<li>
<p><strong>Objectif</strong> : Centraliser la logique de gestion des paramètres d’URL pour les filtres et la pagination, avec des valeurs par défaut et du throttling.</p>
</li>
</ul>
<p>TypeScript</p>
<pre><code>// src/features/utilisateur/filters/utilisateur.filters.ts (Extrait)
import { parseAsString, parseAsInteger, parseAsStringEnum } from 'nuqs';
import { UtilisateurRole, UtilisateurStatus, UtilisateurType } from '@/features/utilisateur/types/utilisateur.type';
import { getEnumValues } from '@/utils/getEnumValues';

export const utilisateurFiltersClient = {
    filter: {
        type: parseAsStringEnum&lt;UtilisateurType&gt;(getEnumValues(UtilisateurType)).withDefault(UtilisateurType.PERSONNEL),
        status: parseAsStringEnum&lt;UtilisateurStatus&gt;(getEnumValues(UtilisateurStatus)).withDefault(UtilisateurStatus.ACTIVE),
        role: parseAsStringEnum&lt;UtilisateurRole&gt;(getEnumValues(UtilisateurRole)),
        firstName: parseAsString.withDefault(''),
        // ... autres filtres
        page: parseAsInteger.withDefault(1),
        limit: parseAsInteger.withDefault(10),
    },
    option: {
        clearOnDefault: true,
        throttleMs: 500, // 500ms de délai pour les filtres textuels
    }
};

</code></pre>
<h4 id="hooks-viewmodels-"><code>hooks/</code> (ViewModels) :</h4>
<ul>
<li>
<p><strong>Fichiers</strong> : <code>use[NomFonctionnalite]Logic.ts</code> ou <code>use[NomFonctionnalite].ts</code> (ex: <code>useProductForm.ts</code>, <code>useUtilisateurListTable.ts</code>).</p>
</li>
<li>
<p><strong>Contenu</strong> : Hooks personnalisés qui encapsulent la logique de présentation et la gestion des états de l’interface utilisateur. Ils sont le <strong>ViewModel</strong> de notre architecture.</p>
</li>
<li>
<p><strong>Responsabilité</strong> : Ils préparent les données pour la Vue, gèrent les interactions UI complexes et peuvent appeler les hooks de <code>queries/</code> ou <code>actions/</code>.</p>
</li>
<li>
<p><strong>Quand l’utiliser</strong> : Créez un ViewModel (un hook) lorsque la logique métier de l’IU devient complexe et doit être isolée du composant de la Vue.</p>
</li>
</ul>
<p>TypeScript</p>
<pre><code>// src/features/utilisateur/hooks/useUtilisateurListTable.ts (Extrait)
"use client";
import { useState, useMemo, useCallback } from "react";
import { useReactTable, getCoreRowModel, getSortedRowModel, ColumnDef } from "@tanstack/react-table";
import { useQueryStates } from 'nuqs';
import { utilisateurFiltersClient } from '../filters/utilisateur.filters';
import { useUtilisateursListQuery } from "../queries/utilisateur-list.query";
import { IUtilisateur, IUtilisateursParams, UtilisateurType } from "../types/utilisateur.type";

export function useUtilisateurListTable({ columns, type }: IUtilisateurListTableProps) {
    const [filters, setFilters] = useQueryStates(utilisateurFiltersClient.filter, utilisateurFiltersClient.option);

    const currentSearchParams: IUtilisateursParams = useMemo(() =&gt; {
        return {
            page: filters.page, limit: filters.limit, email: filters.email || undefined,
            type: type === "personnel" ? UtilisateurType.PERSONNEL : UtilisateurType.DEMANDEUR,
            status: filters.status, role: filters.role || undefined,
        };
    }, [filters, type]);

    const { data, isLoading, isError, error, isFetching } = useUtilisateursListQuery(currentSearchParams);
    const users = data?.data || [];
    const totalPages = data?.meta?.totalPages || 1;

    // ... gestion des états des modales et des handlers d'action

    const table = useReactTable({
        data: users, columns, getCoreRowModel: getCoreRowModel(), getSortedRowModel: getSortedRowModel(),
        manualPagination: true, pageCount: totalPages,
        state: { pagination: { pageIndex: (filters.page || 1) - 1, pageSize: filters.limit || 10 } },
        onPaginationChange: (updater) =&gt; { /* ... met à jour les filtres Nuqs */ },
        meta: { onEdit: handleEditUser, onDelete: handleDeleteUser, onLockUnlock: handleLockUnlockUser },
    });

    return { table, isLoading, isError, error, isFetching, filters, /* ... modalStates, modalHandlers, currentUser */ };
}

</code></pre>
<h4 id="queries-tanstack-query-"><code>queries/</code> (TanStack Query) :</h4>
<ul>
<li>
<p><strong>Fichiers</strong> : <code>[queryName].query.ts</code> (ex: <code>getAllProducts.query.ts</code>, <code>utilisateur-list.query.ts</code>), <code>[mutationName].mutation.ts</code> (ex: <code>createProduct.mutation.ts</code>, <code>utilisateur.mutation.ts</code>), <code>index.query.ts</code> (pour les clés de cache et l’invalidation).</p>
</li>
<li>
<p><strong>Contenu</strong> : Hooks <code>useQuery</code> et <code>useMutation</code> de TanStack Query.</p>
</li>
<li>
<p><strong>Objectif</strong> : Gérer le cache, les re-fetches, les chargements, les erreurs pour les opérations de lecture et de mutation.</p>
</li>
<li>
<p><strong>Convention</strong> : Ces hooks <strong>DOIVENT</strong> appeler les Server Actions de <code>features/[module]/actions/</code> pour interagir avec le backend, et non directement les fonctions de <code>api/</code>.</p>
</li>
</ul>
<p>TypeScript</p>
<pre><code>// src/features/utilisateur/queries/utilisateur-list.query.ts (Extrait)
// Hook pour récupérer la liste des utilisateurs avec pagination et filtres
import { useQuery } from '@tanstack/react-query';
import getQueryClient from '@/lib/get-query-client';
import { obtenirTousUtilisateursAction } from '../actions/utilisateur.action';
import { IUtilisateursParams } from '../types/utilisateur.type';
import { utilisateurKeyQuery } from './index.query';

const queryClient = getQueryClient();

export const utilisateursListQueryOption = (utilisateursParamsDTO: IUtilisateursParams) =&gt; {
    return {
        queryKey: utilisateurKeyQuery("list", utilisateursParamsDTO),
        queryFn: async () =&gt; {
            return obtenirTousUtilisateursAction(utilisateursParamsDTO);
        },
        keepPreviousData: true, staleTime: 30 * 1000, refetchOnWindowFocus: false,
    };
};

export const useUtilisateursListQuery = (utilisateursParamsDTO: IUtilisateursParams) =&gt; {
    return useQuery(utilisateursListQueryOption(utilisateursParamsDTO));
};

export const prefetchUtilisateursListQuery = (utilisateursParamsDTO: IUtilisateursParams) =&gt; {
    return queryClient.prefetchQuery(utilisateursListQueryOption(utilisateursParamsDTO));
}

</code></pre>
<p>TypeScript</p>
<pre><code>// src/features/utilisateur/queries/utilisateur.mutation.ts (Extrait)
// Hooks de mutation pour les opérations CRUD sur les utilisateurs
import { useMutation } from '@tanstack/react-query';
import { ajouterUtilisateurAction } from '../actions/utilisateur.action';
import { useInvalidateUtilisateurQuery } from './index.query';
import { UtilisateurAddDTO } from '../schema/utilisateur.schema';
import { toast } from "sonner";

export const useAjouterUtilisateurMutation = () =&gt; {
    const invalidateUtilisateurQuery = useInvalidateUtilisateurQuery()
    return useMutation({
        mutationFn: async (data: UtilisateurAddDTO) =&gt; ajouterUtilisateurAction(data),
        onSuccess: async () =&gt; {
            await invalidateUtilisateurQuery();
            toast.success("Utilisateur ajouté avec succès");
        },
        onError: async (error) =&gt; {
            toast.error("Erreur lors de l'ajout de l'utilisateur:", { description: error.message });
        },
    });
};
// ... autres mutations (modifier, supprimer, activer/désactiver)

</code></pre>
<h4 id="schemas-zod-schemas--dtos-"><code>schemas/</code> (Zod Schemas &amp; DTOs) :</h4>
<ul>
<li>
<p><strong>Fichiers</strong> : <code>[schemaName].schema.ts</code> (ex: <code>product.schema.ts</code>, <code>utilisateur.schema.ts</code>).</p>
</li>
<li>
<p><strong>Contenu</strong> : Définitions des schémas de validation avec <a href="https://zod.dev/">Zod</a>.</p>
</li>
<li>
<p><strong>Types DTO</strong> : Les types TypeScript générés à partir de ces schémas (<code>z.infer&lt;typeof someSchema&gt;</code>) sont nos <strong>DTOs (Data Transfer Objects)</strong>, assurant la cohérence des données avec le backend.</p>
</li>
</ul>
<p>TypeScript</p>
<pre><code>// src/features/utilisateur/schema/utilisateur.schema.ts (Extrait)
import { z } from 'zod';
import { UtilisateurRole } from '../types/utilisateur.type';

export const UtilisateurAddSchema = z.object({
    firstName: z.string({ message: "Le prénom est requis" }).min(2, "Le prénom doit contenir au moins 2 caractères").trim(),
    lastName: z.string({ message: "Le nom est requis" }).min(2, "Le nom doit contenir au moins 2 caractères").trim(),
    email: z.string({ message: "L'email est requis" }).email("L'email doit être une adresse valide").toLowerCase().trim(),
    phoneNumber: z.string({ message: "Le numéro de téléphone est requis" }).max(20, "Le numéro de téléphone ne doit pas dépasser 20 caractères").regex(/^\+?[\d\s\-]+$/, "Numéro de téléphone invalide").trim(),
    role: z.enum(['AGENT', 'CHEF_SERVICE', 'CONSUL', 'ADMIN'], { message: "Le rôle est requis" })
});

export type UtilisateurAddDTO = z.infer&lt;typeof UtilisateurAddSchema&gt;;
export const UtilisateurUpdateSchema = UtilisateurAddSchema.partial();
export type UtilisateurUpdateDTO = z.infer&lt;typeof UtilisateurUpdateSchema&gt;;

</code></pre>
<h4 id="tests-"><code>tests/</code> :</h4>
<ul>
<li>
<p><strong>Contenu</strong> : Tests unitaires et d’intégration pour les éléments du module.</p>
</li>
<li>
<p><strong>Objectif</strong> : Assurer la qualité et la fiabilité du code.</p>
</li>
</ul>
<h4 id="types-"><code>types/</code> :</h4>
<ul>
<li>
<p><strong>Fichiers</strong> : <code>[typeName].d.ts</code> ou <code>[typeName].ts</code> (ex: <code>product.d.ts</code>, <code>utilisateur.type.ts</code>).</p>
</li>
<li>
<p><strong>Contenu</strong> : Types TypeScript spécifiques à ce module qui ne sont pas dérivés des schémas Zod. Par exemple, des interfaces pour les props de composants internes, des types pour des états complexes, des enums spécifiques au module.</p>
</li>
</ul>
<p>TypeScript</p>
<pre><code>// src/features/utilisateur/types/utilisateur.type.ts (Extrait)
export enum UtilisateurRole { AGENT = "AGENT", CHEF_SERVICE = "CHEF_SERVICE", CONSUL = "CONSUL", ADMIN = "ADMIN" }
export enum UtilisateurType { DEMANDEUR = "DEMANDEUR", PERSONNEL = "PERSONNEL" }
export enum UtilisateurStatus { ACTIVE = "ACTIVE", INACTIVE = "INACTIVE", DELETED = "DELETED" }

export interface IUtilisateur {
    id: string; email: string; firstName: string; lastName: string; phoneNumber: string;
    role: UtilisateurRole; type: UtilisateurType; status: UtilisateurStatus;
    isPasswordChangeRequired: boolean; createdAt: string; updatedAt: string; deletedAt: string;
    // ... autres propriétés optionnelles (requests, uploadedDocuments, etc.)
}
export interface IUtilisateursParams { /* ... paramètres de recherche ... */ }
export interface IUtilisateurStatsResponse { /* ... structure des stats ... */ }
// ... autres interfaces de réponse

</code></pre>
<h4 id="utils-et-helpers-utilitaires-du-module-"><code>utils/</code> et <code>helpers/</code> (Utilitaires du Module) :</h4>
<ul>
<li>
<p><strong>Contenu</strong> : Fonctions utilitaires pures, spécifiques à ce module (ex: <code>formatProductPrice</code>, <code>getUtilisateurRole</code>).</p>
</li>
<li>
<p><strong>Objectif</strong> : Isoler la logique réutilisable et testable propre au module.</p>
</li>
</ul>
<h3 id="dossiers-à-la-racine-components-hooks-lib-styles-types">3.3 Dossiers à la racine (<code>components/</code>, <code>hooks/</code>, <code>lib/</code>, <code>styles/</code>, <code>types/</code>)</h3>
<p>Ces dossiers sont réservés aux éléments globaux, réutilisables dans toute l’application et non spécifiques à une seule fonctionnalité.</p>
<ul>
<li>
<p><strong><code>components/</code></strong> : Composants React génériques et réutilisables partout (ex: <code>Button</code>, <code>Modal</code>, <code>Input</code>). Peut contenir des sous-dossiers pour l’organisation (<code>components/ui</code>, <code>components/layout</code>, <code>components/navigation</code>, <code>components/blocks</code>).</p>
</li>
<li>
<p><strong><code>hooks/</code></strong> : Hooks personnalisés génériques et réutilisables partout (ex: <code>useDebounce</code>, <code>useLocalStorage</code>).</p>
</li>
<li>
<p><strong><code>lib/</code></strong> : Utilitaires globaux (ex: <code>api.ts</code> pour la configuration axios, <code>get-query-client.ts</code> pour la configuration du client TanStack Query, <code>utils/</code> pour des fonctions globales comme <code>formatDate</code>).</p>
</li>
</ul>
<p>TypeScript</p>
<pre><code>// src/lib/api.ts (Extrait)
import { Api } from "ak-api-http"; // Votre bibliothèque de client HTTP
import { baseURL } from "@/config";
import { getTokenInfo, logout } from "@/features/auth/actions/auth.action";

export const api = new Api({
    baseUrl: baseURL, timeout: 10000, headers: { "Content-Type": "application/json" },
    enableAuth: true,
    getSession: async () =&gt; { const jwt = await getTokenInfo(); return { accessToken: jwt?.accessToken ?? "" }; },
    signOut: logout,
    debug: process.env.NODE_ENV === "development",
});

</code></pre>
<p>TypeScript</p>
<pre><code>// src/lib/get-query-client.ts (Extrait)
import { QueryClient } from '@tanstack/react-query';
import { cache } from 'react';

const getQueryClient = cache(() =&gt; new QueryClient({
    defaultOptions: {
        queries: {
            staleTime: 60 * 1000, // 1 minute
            gcTime: 5 * 60 * 1000, // 5 minutes
            retry: (failureCount, error: any) =&gt; { /* ... */ return failureCount &lt; 3; },
        },
    },
}));
export default getQueryClient;

</code></pre>
<ul>
<li>
<p><strong><code>styles/</code></strong> : Styles globaux (Tailwind config, globales CSS) et peut contenir des sous-dossiers pour des styles spécifiques à un module s’ils sont gérés globalement (ex: <code>styles/products.module.css</code>).</p>
</li>
<li>
<p><strong><code>types/</code></strong> : Types TypeScript globaux ou partagés entre plusieurs modules qui ne sont pas des DTOs et ne sont pas spécifiques à un seul module (ex: <code>CommonProps.d.ts</code>, <code>GlobalEnums.d.ts</code>, <code>PaginatedResponse.d.ts</code>).</p>
</li>
</ul>
<hr>
<h2 id="flux-de-données-et-sécurité-next.js--tanstack-query">4. Flux de Données et Sécurité (Next.js &amp; TanStack Query)</h2>
<p>Le flux de données suit un chemin clair et sécurisé, en tirant parti des capacités de Next.js et TanStack Query :</p>
<ol>
<li>
<p><strong>Vue (Composant React)</strong> : Rend l’UI et interagit avec les <code>hooks/</code> du ViewModel.</p>
</li>
<li>
<p><strong>ViewModel (Hooks dans <code>features/[module]/hooks/</code>)</strong> : Encapsule la logique de présentation et l’état du composant. Il utilise les hooks de <code>queries/</code> ou <code>actions/</code> pour interagir avec les données.</p>
</li>
<li>
<p><strong>Queries (<code>features/[module]/queries/</code>) / Mutations (<code>features/[module]/queries/</code>)</strong> :</p>
<ul>
<li>
<p>Utilisent <code>useQuery</code> ou <code>useMutation</code> de TanStack Query.</p>
</li>
<li>
<p>Point crucial de sécurité et d’optimisation : Ces hooks <strong>DOIVENT</strong> appeler les <strong>Server Actions</strong> de <code>features/[module]/actions/</code> pour toutes les interactions avec le backend (lecture et mutation). C’est le point d’entrée pour la récupération/modification des données côté client, garantissant que les informations sensibles du backend ne sont pas exposées.</p>
</li>
</ul>
</li>
<li>
<p><strong>Server Actions (<code>features/[module]/actions/</code>)</strong> :</p>
<ul>
<li>
<p>Fonctions s’exécutant sur le serveur (“use server”). Elles reçoivent les requêtes du client (via TanStack Query) et délèguent l’appel aux fonctions API brutes de <code>features/[module]/api/</code>.</p>
</li>
<li>
<p>C’est ici que vous pouvez ajouter des logiques de validation côté serveur (comme avec <a href="https://www.google.com/search?q=https://www.npmjs.com/package/ak-zod-form-kit">ak-zod-form-kit</a>) ou de sécurité supplémentaires avant d’appeler l’API.</p>
</li>
</ul>
</li>
<li>
<p><strong>API (<code>features/[module]/api/</code>)</strong> :</p>
<ul>
<li>
<p>Contiennent les fonctions qui exécutent les requêtes HTTP brutes via le client configuré dans <code>lib/api.ts</code>.</p>
</li>
<li>
<p>Ces fonctions sont des wrappers directs autour des appels HTTP (ex: <code>axios.get</code>, <code>axios.post</code>).</p>
</li>
</ul>
</li>
<li>
<p><strong><code>lib/api.ts</code></strong> :</p>
<ul>
<li>Configure le client HTTP (<a href="https://www.google.com/search?q=https://www.npmjs.com/package/ak-api-http">ak-api-http</a> basé sur <a href="https://axios-http.com/fr/docs/intro">Axios</a>) avec les intercepteurs pour la gestion globale des requêtes/réponses, l’ajout de headers d’authentification, la gestion centralisée des erreurs, etc.</li>
</ul>
</li>
</ol>
<hr>
<h2 id="technologies-utilisées">5. Technologies Utilisées</h2>
<h3 id="framework-principal">Framework Principal</h3>
<ul>
<li>
<p><strong>Next.js 14</strong> - Framework React avec App Router</p>
</li>
<li>
<p><strong>React 18</strong> - Bibliothèque UI</p>
</li>
<li>
<p><strong>TypeScript</strong> - Typage statique</p>
</li>
</ul>
<h3 id="styling-et-ui">Styling et UI</h3>
<ul>
<li>
<p><strong>Tailwind CSS</strong> - Framework CSS utilitaire</p>
</li>
<li>
<p><strong>shadcn/ui</strong> - Composants UI réutilisables (construits sur <a href="https://www.radix-ui.com/">Radix UI</a>)</p>
</li>
<li>
<p><strong>Radix UI</strong> - Composants primitifs accessibles</p>
</li>
<li>
<p><strong>Lucide React</strong> - Icônes</p>
</li>
<li>
<p><strong>@heroui/react</strong> - Bibliothèque de composants UI</p>
</li>
<li>
<p><strong>sonner</strong> - Pour les notifications toast</p>
</li>
</ul>
<h3 id="gestion-détat-et-données">Gestion d’État et Données</h3>
<ul>
<li>
<p><strong>TanStack Query</strong> - Gestion des requêtes et cache</p>
</li>
<li>
<p><strong>Jotai</strong> - Gestion d’état atomique (si utilisé pour l’état global)</p>
</li>
<li>
<p><strong>React Hook Form</strong> - Gestion des formulaires</p>
</li>
<li>
<p><strong>Zod</strong> - Validation de schémas</p>
</li>
<li>
<p><strong>nuqs</strong> - Synchronisation des paramètres d’URL avec l’état React</p>
</li>
<li>
<p><strong>ak-api-http</strong> - Client HTTP basé sur Axios pour les requêtes API</p>
</li>
<li>
<p><strong>ak-zod-form-kit</strong> - Utilitaire pour la validation de données de formulaire basée sur Zod</p>
</li>
</ul>
<h3 id="authentification-et-sécurité">Authentification et Sécurité</h3>
<ul>
<li>
<p><strong>NextAuth.js</strong> - Authentification complète (si intégré)</p>
</li>
<li>
<p><strong>Server Actions</strong> - Pour les opérations côté serveur sécurisées</p>
</li>
</ul>
<h3 id="internationalisation">Internationalisation</h3>
<ul>
<li>
<p><strong>next-intl</strong> - Support multilingue (EN/AR)</p>
</li>
<li>
<p><strong>RTL Support</strong> - Support des langues de droite à gauche</p>
</li>
</ul>
<h3 id="autres-bibliothèques-notables">Autres Bibliothèques Notables</h3>
<ul>
<li>
<p><strong>@tanstack/react-table</strong> - Tableaux de données avancés</p>
</li>
<li>
<p><strong>FullCalendar</strong> - Calendrier interactif (si intégré)</p>
</li>
<li>
<p><strong>ApexCharts / Chart.js / Recharts</strong> - Pour les graphiques et visualisations (si intégrés)</p>
</li>
</ul>
<hr>
<h2 id="installation-et-configuration">6. Installation et Configuration</h2>
<h3 id="prérequis">Prérequis</h3>
<ul>
<li>
<p>Node.js 18+</p>
</li>
<li>
<p>pnpm (recommandé) ou npm</p>
</li>
<li>
<p>Eslint (obligatoire)</p>
</li>
</ul>
<h3 id="installation">Installation</h3>
<ol>
<li>
<p><strong>Cloner le projet</strong></p>
<p>Bash</p>
<pre><code>git clone &lt;repository-url&gt;
cd start

</code></pre>
</li>
<li>
<p><strong>Installer les dépendances</strong></p>
<p>Bash</p>
<pre><code>pnpm install

</code></pre>
</li>
<li>
<p><strong>Configuration des variables d’environnement</strong></p>
<p>Bash</p>
<pre><code>cp .env.example .env.local

</code></pre>
</li>
<li>
<p><strong>Lancer le serveur de développement</strong></p>
<p>Bash</p>
<pre><code>pnpm dev

</code></pre>
</li>
</ol>
<hr>
<h2 id="approche-de-développement--patterns">7. Approche de Développement / Patterns</h2>
<h3 id="architecture-par-fonctionnalités">Architecture par Fonctionnalités</h3>
<p>Le projet est organisé autour de fonctionnalités métier dans le dossier <code>features/</code>. Cette approche favorise :</p>
<ul>
<li>
<p><strong>Autonomie des Modules</strong> : Chaque fonctionnalité est un mini-domaine avec sa propre logique, types, API, et composants.</p>
</li>
<li>
<p><strong>Clarté</strong> : Facilite la navigation et la compréhension du code pour les nouveaux développeurs.</p>
</li>
<li>
<p><strong>Maintenabilité</strong> : Les changements dans une fonctionnalité ont un impact limité sur les autres.</p>
</li>
<li>
<p><strong>Scalabilité</strong> : Ajout de nouvelles fonctionnalités sans affecter l’organisation existante.</p>
</li>
</ul>
<h3 id="patterns-utilisés">Patterns Utilisés</h3>
<ul>
<li>
<p><strong>Feature-First Architecture</strong> : Organisation du code autour des fonctionnalités métier.</p>
</li>
<li>
<p><strong>MVVM (Model-View-ViewModel)</strong> : Pour la séparation de la logique de présentation et de l’UI.</p>
</li>
<li>
<p><strong>Provider Pattern</strong> : Gestion d’état globale avec React Context ou d’autres providers (comme TanStack Query Provider).</p>
</li>
<li>
<p><strong>Custom Hooks</strong> : Encapsulation de la logique métier réutilisable et de la gestion d’état.</p>
</li>
<li>
<p><strong>Component Composition</strong> : Construction de composants modulaires et réutilisables à partir de composants plus petits.</p>
</li>
<li>
<p><strong>Type-Safe Development</strong> : Utilisation stricte de TypeScript avec Zod pour la validation des données, garantissant la sécurité des types à travers l’application.</p>
</li>
</ul>
<h3 id="structure-des-composants">Structure des Composants</h3>
<ul>
<li>
<p><strong>UI Components</strong> (<code>components/ui/</code>) : Composants de base, génériques et stylisés (souvent issus de shadcn/ui ou Radix UI). Ils sont “stupides” et ne contiennent pas de logique métier.</p>
</li>
<li>
<p><strong>Partial Components</strong> (<code>components/partials/</code>) : Composants spécifiques à la structure de l’interface globale (ex: Header, Sidebar, Footer).</p>
</li>
<li>
<p><strong>Block Components</strong> (<code>components/blocks/</code>) : Composants de contenu réutilisables qui peuvent contenir une certaine logique de présentation mais sont agnostiques au module métier (ex: StatusBlock).</p>
</li>
<li>
<p><strong>Feature Components</strong> (<code>features/[module]/components/</code>) : Composants liés spécifiquement à une fonctionnalité métier. Ils peuvent contenir de la logique métier propre à leur module et sont souvent des “Client Components” qui interagissent avec les ViewModels (hooks) de leur module.</p>
</li>
</ul>
</div>
</body>

</html>
