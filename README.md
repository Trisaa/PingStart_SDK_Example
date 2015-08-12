# PingStart_SDK_Example

[PingStart Android SDK Example Project](http://pingstart.com)


#####1.	You need to register on [PingStart](http://www.pingstart.com/login).

#####2.	Creat your Ads Slot on the website to get your App ID and your Slot ID.

#####3.	Apply for two permissions on the AndroidManafast.xml:

		<uses-permission android:name="android.permission.INTERNET" />

		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

#####4. Register the OptimizeService and OptimizeReceiver on the AndroidManafast.xml :

		<service android:name="com.pingstart.adsdk.OptimizeService"/>
		<receiver android:name="com.pingstart.adsdk.OptimizeReceiver" > 
			<intent-filter>
			<action android:name="android.net.conn.CONNECTIVITY_CHANGE" /> 
			</intent-filter>
		</receiver>

#####5. Add the following code at the top of your activity class in order to import the Ads SDK:

		import com.pingstart.adsdk.AdsClickListener;
		import com.pingstart.adsdk.AdsLoadListener;
		import com.pingstart.adsdk.AdsManager;
		
		//Then, create a function that requests the PingStart ads:
		private AdsManager mAdsManager;
		
		private void loadAds(){
			mAdsManager = new AdsManager(this, {Your App ID}, {Your Slot ID});
			mAdsManager.loadAds(new AdsLoadListener() {
			
				@Override
				public void onLoadBannerSucceeded() {
					/**
					*	If you want to implement the Banner style,
					*	you should creat a ViewGroup, and get the bannerView,
					*	then add it to ViewGroup
					*/
					RelativeLayout viewGroup = new RelativeLayout(mContext); 
					View bannerView = mAdsManager.getBannerView(); 
					viewGroup.addView(bannerView);
				}
				
				@Override
				public void onLoadNativeSucceeded(Ads ad) {
					/**
					*	If you want to implement the Native style,
					*	you should get all elements of native ad,
					*	customize your native UI and register your NativeView
					*/
					String titleForAd = ad.getTitle();
					String titleForAdButton = ad.getAdCallToAction(); 
					String 	descriptionForAd = 	ad.getDescription();
					double appRatingForAd = ad.getRatings();

					//	Add code here to create a custom view that uses the ad properties
					//	For example:
					LinearLayout nativeAdContainer = new LinearLayout(mContext);
					TextView titleLabel = new TextView(mContext);
					titleLabel.setText(titleForAd);
					nativeAdContainer.addView(titleLabel);
					
					//	Load the cover image into an ImageView using an helper function 	
					ImageView iconImage = new ImageView(mContext);
					ImageView coverImage = new ImageView(mContext);
					
					//	Method downloadAndDisplayImage(String imageUrl, ImageView imageView) will be 						//	implemented at the end of this PDF 
					downloadAndDisplayImage(ad.getIcon_link(), iconImage);
					downloadAndDisplayImage(ad.getPreview_link(), coverImage);
					
					//	Add the ad to your layout
					LinearLayout mainContainer = (LinearLayout)findViewById(R.id.v); 		
					mainContainer.addView(nativeAdContainer);
					
					// Register the native ad view with the native ad instance   	
					mAdsManager.registerNativeView(nativeAdContainer,new AdsClickListener()
					{
						@Override
						public void onError() {
							// this function will be called when the ad fails to open.
						}
						@Override
						public void onAdsClicked() {
							// this function will be called when the ad opens successfully.
						}
					});
				}
				
				@Override
				public void onLoadInterstitialSucceeded() { 
					/**
					*	If you want to implement the Interstitial style,
					*	you should call the showInterstitialAd().
					*/ manager.showInterstitialAd();
				}
				
				@Override
				public void onLoadError() { 
					/**
					* This function will be called when SDK fails to load. */
				}
			}, Your-AdType);
		}
		
		You could replace Your-AdType with 
			* AdsManager.BANNER_AD		
			* AdsManager.INTERSTITIAL_AD 
			* AdsManager.NATIVE_AD


#####6.	You need to call the destroy() function in your Activity onDestroy :

		@Override
		protected void onDestroy() {
			mAdsManager.destroy();
			super.onDestroy();
		}


#####7.	If you want to use the method downloadAndDisplayImage() to download the icon image and cover image, you could copy these following code:

		private void downloadAndDisplayImage(String imageUrl, final ImageView imageView) { 
			RequestQueue imageRequestQueue = Volley.newRequestQueue(mContext); 			
			ImageRequest imageRequest = new ImageRequest(imageUrl,new
				Response.Listener<Bitmap>() {
				
					@Override
					public void onResponse(Bitmap response) {
						imageView.setImageBitmap(response);
					}
					
				}, 0, 0, Config.RGB_565, new Response.ErrorListener() {
					@Override
					public void onErrorResponse(VolleyError error) {
						imageView.setImageResource(R.drawable.default_image);
				}
				
			});
			imageRequestQueue.add(imageRequest);
		}
