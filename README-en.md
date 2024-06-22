English Vision | [中文版](README-zh.md)

## DYFStoreReceiptVerifier

An open source receipt verification program for iOS([Swift Version](https://github.com/itenfay/DYFStoreReceiptVerifier_Swift)). 

It is recommended that use your own server to obtain the parameters uploaded from the client to verify the receipt from the App Store server (C -> Uploaded Parameters -> S -> App Store S -> S -> Receive And Parse Data -> C, C: client, S: server).

[![License MIT](https://img.shields.io/badge/license-MIT-green.svg?style=flat)](LICENSE)&nbsp;
[![CocoaPods Version](http://img.shields.io/cocoapods/v/DYFStoreReceiptVerifier.svg?style=flat)](http://cocoapods.org/pods/DYFStoreReceiptVerifier)&nbsp;
![CocoaPods Platform](http://img.shields.io/cocoapods/p/DYFStoreReceiptVerifier.svg?style=flat)&nbsp;


## Group (ID:614799921)

<div align=left>
&emsp; <img src="https://github.com/itenfay/DYFStoreReceiptVerifier/raw/master/images/g614799921.jpg" width="30%" />
</div>


## Installation

Using [CocoaPods](https://cocoapods.org):

```
pod 'DYFStoreReceiptVerifier'
```

Or

```
pod 'DYFStoreReceiptVerifier', '~> 1.2.1'
```


## Usage

- The URLs for verification.

1. Sandbox URL: `https://sandbox.itunes.apple.com/verifyReceipt` <br>
2. Production URL: `https://buy.itunes.apple.com/verifyReceipt`

- The reference verifier.

You create and return a receipt verifier(`DYFStoreReceiptVerifier`) by using lazy loading.

```
- (DYFStoreReceiptVerifier *)receiptVerifier {
    if (!_receiptVerifier) {
        _receiptVerifier = [[DYFStoreReceiptVerifier alloc] init];
        _receiptVerifier.delegate = self;
    }
    return _receiptVerifier;
}
```

- The verifier delegates receipt verification.

1. Using the `DYFStoreReceiptVerifierDelegate` protocol:

```
- (void)verifyReceiptDidFinish:(nonnull DYFStoreReceiptVerifier *)verifier didReceiveData:(nullable NSDictionary *)data;
- (void)verifyReceipt:(nonnull DYFStoreReceiptVerifier *)verifier didFailWithError:(nonnull NSError *)error;
```

2. You provide your own implementation.

```
- (void)verifyReceiptDidFinish:(nonnull DYFStoreReceiptVerifier *)verifier didReceiveData:(nullable NSDictionary *)data {
    // Writes the implementation codes.
}

- (void)verifyReceipt:(nonnull DYFStoreReceiptVerifier *)verifier didFailWithError:(nonnull NSError *)error {
    // Writes the implementation codes.
}
```

- Verifies the receipt.

- Step1:

Fetches the data of the bundle’s App Store receipt.

```
// receiptData: the data of the bundle’s App Store receipt. 
NSData *data = receiptData ?: [NSData dataWithContentsOfURL:DYFStore.receiptURL];
DYFStoreLog(@"data: %@", data);
```

- Step2:

Verifies the in-app purchase receipt.

```
[_receiptVerifier verifyReceipt:data];
```

Your app’s shared secret (a hexadecimal string). Only used for receipts that contain auto-renewable subscriptions.

```
[_receiptVerifier verifyReceipt:data sharedSecret:@"A43512564ACBEF687924646CAFEFBDCAEDF4155125657"];
```

- The status code and description.

```
/**
 Matches the message with the status code.
 
 @param status The status code of the request response. More, please see [Receipt Validation Programming Guide](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html#//apple_ref/doc/uid/TP40010573-CH104-SW1)
 @return A string that contains the description of status code.
 */
- (NSString *)matchMessageWithStatus:(NSInteger)status {
    NSString *message = @"";
    switch (status) {
        case 0:
            message = @"The receipt as a whole is valid.";
            break;
        case 21000:
            message = @"The App Store could not read the JSON object you provided.";
            break;
        case 21002:
            message = @"The data in the receipt-data property was malformed or missing.";
            break;
        case 21003:
            message = @"The receipt could not be authenticated.";
            break;
        case 21004:
            message = @"The shared secret you provided does not match the shared secret on file for your account.";
            break;
        case 21005:
            message = @"The receipt server is not currently available.";
            break;
        case 21006:
            message = @"This receipt is valid but the subscription has expired. When this status code is returned to your server, the receipt data is also decoded and returned as part of the response. Only returned for iOS 6 style transaction receipts for auto-renewable subscriptions.";
            break;
        case 21007:
            message = @"This receipt is from the test environment, but it was sent to the production environment for verification. Send it to the test environment instead.";
            break;
        case 21008:
            message = @"This receipt is from the production environment, but it was sent to the test environment for verification. Send it to the production environment instead.";
            break;
        case 21010:
            message = @"This receipt could not be authorized. Treat this the same as if a purchase was never made.";
            break;
        default: /* 21100-21199 */
            message = @"Internal data access error.";
            break;
    }
    return message;
}
```


## Requirements

`DYFStoreReceiptVerifier` requires `iOS 7.0` or above and `ARC`.


## Demo

To learn more, please check out [Demo](https://github.com/itenfay/DYFStoreKit/blob/master/DYFStoreKitDemo/Sample/SKIAPManager.m).


## Feedback is welcome

If you notice any issue to create an issue. I will be happy to help you.
