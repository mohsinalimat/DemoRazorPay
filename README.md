# DemoRazorPay
Sample app demonstrating integration of Razorpay iOS Framework

Usage Instructions#

Step 1: Import library to the project#

Download the SDK from above and unzip it.
Open your project in XCode and go to file under Menu. Select Add files to "yourproject".
Select Razorpay.framework in the directory you just unzipped.
Select the Copy items if needed check-box.
Click Add.
If you are building an Objective-C project, follow the additional steps given below:

Go to Project Settings.
Select Build Settings - All and Combined from the Menu of the project settings.
Under Build Options set Always Embed Swift Standard Libraries option to TRUE.
For both iOS and Objective C projects - Make sure the framework is added in both the Embeded Binaries and Linked Frameworks and Libraries under Target settings - General.
Step 2: Initialize the Razorpay SDK#

To initialize the Razorpay SDK, you will need the following:

API key.
A delegate that implements RazorpayPaymentCompletionProtocol or RazorpayPaymentCompletionProtocolWithData.
ViewController.swift

import Razorpay

class ViewController: UIViewController, RazorpayPaymentCompletionProtocol {

	var razorpay: Razorpay!
	.
	.
	override func viewDidLoad() {
        super.viewDidLoad()
        .
        .
        razorpay = Razorpay.initWithKey(razorpayTestKey, andDelegate: self)
    }
    .
    .
}
ViewController.m
#import <Razorpay/Razorpay.h>

@interface ViewController () <RazorpayPaymentCompletionProtocol> {
  Razorpay *razorpay;
	.  
	. 
	- (void)viewDidLoad {
  		[super viewDidLoad];
  		.
  		.
  		razorpay = [Razorpay initWithKey:@"YOUR_PUBLIC_KEY" andDelegate:self];
	}
}
Step 3: Pass payment options and show the Checkout Form#

Add the following code to your ViewController or where ever you want to initialize payments:

ViewController.swift
internal func showPaymentForm(){
	let options: [String:Any] = [
				"amount" : "100" //mandatory in paise
            	"description": "purchase description"
            	"image": "https://url-to-image.png",
            	"name": "business or product name"
            	"prefill": [
                	"contact": "9797979797",
                	"email": "foo@bar.com"
                ],
                "theme": [
                    "color": "#F37254"
              	]
        	]
	razorpay.open(options)
}
ViewController.m
- (void)showPaymentForm { // called by your app
  NSDictionary *options = @{
                            @"amount": @"1000", // mandatory, in paise
                            // all optional other than amount.
                            @"image": @"https://url-to-image.png",
                            @"name": @"business or product name",
                            @"description": @"purchase description",
                            @"prefill" : @{ 
                                @"email": @"foo@bar.com",
                                @"contact": @"9797979797"
                            },
                            @"theme": @{
                                @"color": @"#F37254"
                            }
                        };
    [razorpay open:options];
}
That's pretty much it. You can find the list of all supported options here.

Progress Bar: To support theme color in progress bar, please pass HEX color values only.
Step 4: Handle success/error when payment is done#

You can handle success/errors when payment is completed by implementing onPaymentSuccess and onPaymentError methods of the RazorpayPaymentCompletionProtocol. Alternatively, you can also implement onPaymentSuccess and onPaymentError methods of RazorpayPaymentCompletionProtocolWithData.

ViewController.swift
public func onPaymentError(_ code: Int32, description str: String){
    let alertController = UIAlertController(title: "FAILURE", message: str, preferredStyle: UIAlertControllerStyle.alert)
    let cancelAction = UIAlertAction(title: "OK", style: UIAlertActionStyle.cancel, handler: nil)
    alertController.addAction(cancelAction)
    self.view.window?.rootViewController?.present(alertController, animated: true, completion: nil)
}

public func onPaymentSuccess(_ payment_id: String){
    let alertController = UIAlertController(title: "SUCCESS", message: "Payment Id \(payment_id)", preferredStyle: UIAlertControllerStyle.alert)
    let cancelAction = UIAlertAction(title: "OK", style: UIAlertActionStyle.cancel, handler: nil)
    alertController.addAction(cancelAction)
    self.view.window?.rootViewController?.present(alertController, animated: true, completion: nil)
}
ViewController.m
- (void)onPaymentSuccess:(nonnull NSString*)payment_id {
      [[[UIAlertView alloc] initWithTitle:@"Payment Successful" message:payment_id delegate:self cancelButtonTitle:@"OK" otherButtonTitles:nil] show];
}

- (void)onPaymentError:(int)code description:(nonnull NSString *)str {
      [[[UIAlertView alloc] initWithTitle:@"Error" message:str delegate:self cancelButtonTitle:@"OK" otherButtonTitles:nil] show];
}
Here you have to put necessary actions after payment is completed based on success/error.

Possible values for failure code are:

0: Network error
1: Initialization failure / Unexpected behaviour
2: Payment cancelled by user
Success handler will receive payment_id which you can use later for capturing purposes.

iOS 9 Update#

The iOS 9 has higher requirements for secure URLs. As many Indian banks do not comply with the requirements, you can implement the following as a workaround:

info.plist
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
Add the above to your info.plist file. For more information click here.
