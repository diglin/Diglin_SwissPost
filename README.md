# Diglin Swisspost - Magento 1.x Module for Shipping labels and Barcode or Swisspost

## Description

Allow to create shipping labels and barcode for shipping slips. It doesn't work out of the box as you need to add the generated label to your PDF. See the sample of code below to see how to use it

## Documentation

Configure the API credentials under the menu System > Configuration > Diglin > Swisspost, you will need username, password and franchise License

Here is a sample of code. The principle here is to get the path of the file having the generated barcode picture to add it to your PDF depending on the shipping method code and the tracking code:

```
private function _getShipmentLabel($shipment)
{
	$order          = $shipment->getOrder();
	$labelHelper    = Mage::helper('diglin_swisspost/label');
	$labelSize      = $labelHelper->getLabelSize();
	$shippingHelper = Mage::helper('diglin_swisspost/shippingMethod');
	$allMethods     = $shippingHelper->getAllShippingMethods();
	$shippingMethod = $order->getShippingMethod(true);
	$carrierCode    = $shippingMethod->getCarrierCode();
	if (in_array($carrierCode, $allMethods) == false) {
		return null;
	}
	$shipmentId     = $shipment->getIncrementId();
	$tracks         = $shipment->getAllTracks();
	$trackingCode   = '';
	foreach ($tracks as $track) {
		$trackingCode   = $track->getNumber();
		break;
	}

	if (!$trackingCode) {
		return null;
	}
	
	$trackingCode   = Mage::helper('diglin_swisspost/tracking')->formatTrackingNumberToShippingNumber($trackingCode);
	if ($trackingCode && $labelUrl = $labelHelper->getLabelUrl($shipmentId, $trackingCode)) {
		return $labelUrl;
	}
	return null;
}
```	

## License

Proprietary

## Support & Documentation

- Submit tickets - Contact (fee may apply, we will inform you how): support /at/ diglin.com
- Support is NOT free

## System requirements

- Contact [swssipost](https://www.post.ch) to get an account for the SOAP API
- Magento CE >= 1.6.x to 1.9.x and EE 1.14 (tested and used on this version)
- PHP >= 5.3.2
- PHP SOAP

## Installation

### Via MagentoConnect

NaN

### Manually

```
git clone git@github.com:diglin/Diglin_Swisspost.git
git submodule init
git submodule fetch
```

Then copy the files and folders in the corresponding Magento folders
Do not forget the folder "lib"

### Via modman

- Install [modman](https://github.com/colinmollenhour/modman)
- Use the command from your Magento installation folder: `modman clone git@github.com:diglin/Diglin_Swisspost.git`

#### Via Composer

- Install [composer](http://getcomposer.org/download/)
- Create a composer.json into your project like the following sample:

```
 {
    "require" : {
        "diglin/diglin_swisspost": "1.*"
    },
    "repositories" : [
        {
            "type": "vcs",
            "url": "git@github.com:diglin/Diglin_Swisspost.git"
        }
    ]
 }
 ```
- Then from your composer.json folder: `php composer.phar install` or `composer install`
- Do not pay attention to the yellow messages during composer installation process for this extension

## Uninstall

### Modman

Modman can only remove files. So you can run the command `modman remove Diglin_Swisspost` from your Magento root project.

### Manually

Remove the files or folders located into your Magento installation:
```
app/etc/modules/Diglin_Swisspost.xml
app/code/community/Diglin/Swisspost
```

## Author

* Diglin GmbH
* http://www.diglin.com/
* [@diglin_](https://twitter.com/diglin_)
* [Follow me on github!](https://github.com/diglin)
