<p align="center">
</p>

![Example of a product's customer group customization](doc/asdoria.jpg)

<h1 align="center">Asdoria Product Product Customer Group Plugin</h1>

<p align="center">Manage which user can access certain Products by whitelisting Customer Groups for each item in your catalog </p>

## Features

+ Use your Customer Groups to filter which products your users can access
+ Create a whitelist of Groups for each product

<div style="max-width: 75%; height: auto; margin: auto">


![Example of a product's groups whitelisting](doc/guide.gif)


</div>



<div style="max-width: 75%; height: auto; margin: auto">


Creating a Customer Group and setting which Groups are authorized to access a product




</div>

## Installation

---
1. Add the repository to composer.json

```JSON
"repositories": [
    {
    "type": "git",
    "url": "https://github.com/asdoria/AsdoriaSyliusProductCustomerGroup"
    }
],
```
2. run `composer require asdoria/sylius-product-customer-group-plugin`


3. Add the bundle in `config/bundles.php`. You must put it ABOVE `SyliusGridBundle`

```PHP
Asdoria\SyliusProductCustomerGroupPlugin\AsdoriaSyliusProductCustomerGroupPlugin::class => ['all' => true],
[...]
Sylius\Bundle\GridBundle\SyliusGridBundle::class => ['all' => true],
```

4. Import config in `config/packages/_sylius.yaml`
```yaml
imports:
    - { resource: "@AsdoriaSyliusProductCustomerGroupPlugin/Resources/config/services.yaml" }
```
5. In `src/Entity/Product/Product.php`. Import the following classes, traits and methods.

```PHP
use Sylius\Component\Product\Model\ProductTranslationInterface;
use Sylius\Component\Core\Model\Product as BaseProduct;

use Asdoria\SyliusProductCustomerGroupPlugin\Model\ProductInterface as AsdoriaProductCustomerGroupBundleProductInterface;
use Asdoria\SyliusProductCustomerGroupPlugin\Traits\ProductCustomerGroupsTrait as AsdoriaProductCustomerGroupBundleProductTrait;

class Product extends BaseProduct implements AsdoriaProductCustomerGroupBundleProductInterface
{
    use AsdoriaProductCustomerGroupBundleProductTrait;

    protected function createTranslation(): ProductTranslationInterface
    {
        return new ProductTranslation();
    }  
}
```
7. run `php bin/console do:mi:mi` to update the database schema

8. Add the additional Product xml mapping in `src/Resources/config/doctrine/Product.orm.xml`
```XML
<?xml version="1.0" encoding="UTF-8"?>

<doctrine-mapping xmlns="http://doctrine-project.org/schemas/orm/doctrine-mapping"
                  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                  xsi:schemaLocation="http://doctrine-project.org/schemas/orm/doctrine-mapping
                                      http://doctrine-project.org/schemas/orm/doctrine-mapping.xsd">
    
    <mapped-superclass name="Product">
        <many-to-many field="customerGroups" target-entity="Sylius\Component\Customer\Model\CustomerGroupInterface">
            <join-table name="asdoria_products_customer_groups">
                <join-columns>
                    <join-column name="product_id" referenced-column-name="id" />
                </join-columns>
                <inverse-join-columns>
                    <join-column name="customer_group_id" referenced-column-name="id" />
                </inverse-join-columns>
            </join-table>
        </many-to-many>
    </mapped-superclass>
    
</doctrine-mapping>
```

9. Copy the template override from the plugin directory

From: `[shop_dir]/vendor/asdoria/sylius-product-customer-group-plugin/src/Resources/views/bundles/SyliusShopBundle/Product/Box/_content.html.twig`

To: `[shop_dir]/templates/bundles/SyliusShopBundle/Product/Box/_content.html.twig`

---
## Usage

1. Create or use existing Customer Groups
2. Go to a product's configuration page and enter the "Customer Groups" tab
3. Select any Group that will gain access to the product




