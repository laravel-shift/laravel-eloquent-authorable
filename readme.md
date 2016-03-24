# Laravel Eloquent Authorable

Support des champs created_by et updated_by dans les modèles Eloquent.

## Installation

Inclure le package avec Composer :

```
composer require axn/laravel-eloquent-authorable
```

Ajoutez le service provider au tableau des providers dans `config/app.php` :

```
'Axn\EloquentAuthorable\ServiceProvider',
```

## Utilisation

Pour ajouter la fonctionnalité à un modèle il faut que :

1. le modèle implémente l'interface `Axn\EloquentAuthorable\Authorable`
2. le modèle utilise le trait `Axn\EloquentAuthorable\Authorable`
3. éventuellement indiquer quelles colonnes de la table modifier

## Exemple

```php
use Axn\EloquentAuthorable\Authorable;
use Axn\EloquentAuthorable\AuthorableTrait;

class MonModele extends Eloquent implement Authorable
{
    use AuthorableTrait;
}
```

Dès lors, à chaque création/mise à jour d'une entrée dans la table liée au modèle
les colonnes 'created_by' et 'updated_by' seront automatiquement renseignées
avec l'id de l'utilisateur actuellement authentifié.

De plus, deux relations 1-n inverses (belongs to) vers la table des utilisateurs (celle utilisé
pour l'authentification - voir `config/auth.php`) sont disponibles :

- createdBy()
- updatedBy()

**Exemple avec le "with" d'Eloquent (eager-loading) :**

```php
$monModele = MonModele::with('createdBy', 'updatedBy')->first();
```

**Exemple dans une vue Blade :**

```html
<p>Créé par {{ $monModele->createdBy->name }} ({{ $monModele->createdBy->email }})
et mis à jour par {{ $monModele->updatedBy->name }} ({{ $monModele->updatedBy->email }}).</p>
```

Rien ne vous empêche de définir vos propres relations si vous souhaitez des noms
différents pour celles-ci.

## Paramètrage

### Noms des colonnes

Par défaut ce sont les colonnes 'created_by' et 'updated_by' qui sont renseignées.

Vous pouvez préciser des noms de colonnes différents comme ceci :

```php
use Axn\EloquentAuthorable\Authorable;
use Axn\EloquentAuthorable\AuthorableTrait;

class MonModel extends Eloquent implement Authorable
{
    use AuthorableTrait;

    public $authorable [
        'created_by_column_name' => 'custom_created_by',
        'updated_by_column_name' => 'custom_updated_by',
    ];
}
```

### Activation/désactivation

Vous pouvez désactiver la fonctionnalité comme ceci :

```php
use Axn\EloquentAuthorable\Authorable;
use Axn\EloquentAuthorable\AuthorableTrait;

class MonModel extends Eloquent implement Authorable
{
    use AuthorableTrait;

    public $authorable [
        'set_author_when_creating' => false,
        'set_author_when_updating' => false,
    ];
}
```


## Exemple complet

```php
use Axn\EloquentAuthorable\Authorable;
use Axn\EloquentAuthorable\AuthorableTrait;

class MonModel extends Eloquent implement Authorable
{
    use AuthorableTrait;

    public $authorable [
        'created_by_column_name    => 'custom_created_by',
        'set_author_when_creating' => true,
        'updated_by_column_name'   => 'custom_updated_by',
        'set_author_when_updating' => false,
    ];
}
```