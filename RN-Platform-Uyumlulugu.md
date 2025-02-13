- ### Platforma Özel Stil Kullanımı
    * Tutarlı bir kullanıcı deneyimi için stil tanımlamalarında Platform.select kullanılmalıdır.
    ```jsx
  // kötü
  import { StyleSheet } from 'react-native';

  const styles = StyleSheet.create({
    button: {
      padding: 10,
      borderRadius: 5, // iOS ve Android'de farklı davranabilir.
    },
  });


  // iyi
  import { Platform, StyleSheet } from 'react-native';

  const styles = StyleSheet.create({
    button: {
      padding: 10,
      ...Platform.select({
        ios: {
          borderRadius: 10, // iOS'te daha yuvarlak köşeler.
        },
        android: {
          borderRadius: 5, // Android'de daha az yuvarlak köşeler.
        },
      }),
    },
  });

    ```

### Varsayılan Yazı Tipleri ve Font Kullanımı
* Android ve iOS'ta varsayılan fontlar farklıdır. Her iki platformda da aynı font ailesi kullanılamaz, bu nedenle platforma özgü fontlar kullanılmalıdır.
```jsx
// kötü
const styles = StyleSheet.create({
  text: {
    fontFamily: 'Avenir',
  },
});


// iyi
const CustomText = () => (
  <Text style={styles.text}>Platform Uyumluluğu</Text>
);

const styles = StyleSheet.create({
  text: {
    fontFamily: Platform.select({
      ios: 'Avenir',
      android: 'Roboto',
    }),
    fontSize: 16,
  },
});

```

### Platforma Özel Bileşen kullanımı
* Her iki platformda aynı bileşeni kullanmak uyumsuzluğa neden olabileceği için platforma özel bileşenler kullanırken Platform.select tercih edilmelidir.
```jsx
// kötü
import { TouchableOpacity } from 'react-native';

const MyButton = () => (
  <TouchableOpacity style={{ padding: 10 }}>
    {/* iOS ve Android'de farklı davranabilir, ancak dikkate alınmamış. */}
  </TouchableOpacity>
);


// iyi
import { Platform } from 'react-native';
import { IOSButton, AndroidButton } from './components';

const MyButton = Platform.select({
  ios: () => <IOSButton />, // iOS için özel bileşen.
  android: () => <AndroidButton />, // Android için özel bileşen.
})();

export default MyButton;

```


### Geri Butonu (BackHandler) Yönetimi
* Android cihazlarda fiziksel geri butonu bulunurken, iOS cihazlarda bulunmaz. BackHandler kullanımı platforma uygun şekilde ayarlanmalıdır.
```jsx
// kötü
useEffect(() => {
  const backAction = () => {
    BackHandler.exitApp();
    return true;
  };

  BackHandler.addEventListener('hardwareBackPress', backAction);

  return () => BackHandler.removeEventListener('hardwareBackPress', backAction);
}, []);


// iyi
import { BackHandler, Platform, Alert } from 'react-native';

useEffect(() => {
  if (Platform.OS === 'android') {
    const backAction = () => {
      Alert.alert(
        'Dikkat!', 
        'Çıkmak istiyor musunuz?', [
          { text: 'Hayır', style: 'cancel' },
          { text: 'Evet', onPress: () => BackHandler.exitApp() }
        ]
      );
      return true;
    };

    BackHandler.addEventListener('hardwareBackPress', backAction);

    return () => BackHandler.removeEventListener('hardwareBackPress', backAction);
  }
}, []);
```


### Klavye açılış davranışları (KeyboardAvoidingView) 
* iOS ve Android’de klavyenin açılmasıyla farklı davranışlar sergilendiği için KeyboardAvoidingView kullanılarak platforma özel uyarlamalar yapılmalıdır.
```jsx
// kötü
<KeyboardAvoidingView behavior="padding">
  <TextInput placeholder="Type here" />
</KeyboardAvoidingView>


// iyi
import { KeyboardAvoidingView, Platform, TextInput, StyleSheet } from 'react-native';

const App = () => {
  return (
    <KeyboardAvoidingView
      style={styles.container}
      behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
    >
      <TextInput placeholder="Type here" style={styles.input} />
    </KeyboardAvoidingView>
  );
};


```
### Safe Area Kullanımı (SafeAreaView)
* iPhone X ve sonrası çentikli ekranlarda içeriklerin kesilmesini önlemek için SafeAreaView kullanılmalıdır.
```jsx
// kötü
import { View, Text } from 'react-native';

const UnsafeComponent = () => {
  return (
    <View style={{ flex: 1 }}>
      <Text>Bu içerik çentik tarafından kapatılabilir!</Text>
    </View>
  );
};


// iyi
import { SafeAreaView, Text } from 'react-native';

const SafeComponent = () => {
  return (
    <SafeAreaView style={{ flex: 1 }}>
      <Text>Çentik Uyumu Sağlandı</Text>
    </SafeAreaView>
  );
};


```

### Gölgelendirme Farklılıkları
* Android’de elevation, iOS’ta ise shadowColor, shadowOffset, shadowOpacity, shadowRadius gibi farklı gölgelendirme özellikleri kullanılmalıdır.
```jsx
// kötü
const styles = StyleSheet.create({
  card: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.2,
    shadowRadius: 4,
  },
});


// iyi
const styles = StyleSheet.create({
  card: {
    backgroundColor: '#fff',
    borderRadius: 8,
    padding: 16,
    ...Platform.select({
      ios: {
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 2 },
        shadowOpacity: 0.2,
        shadowRadius: 4,
      },
      android: {
        elevation: 4,
      },
    }),
  },
});

```

### Bildirimler (Push Notifications)
* iOS ve Android’de bildirim yönetimi, expo-notifications veya react-native-push-notification gibi kütüphanelerle platforma uygun konfigürasyonlarla kullanılmalıdır.
```jsx
// kötü
import { Notifications } from 'expo-notifications';

const sendNotification = async () => {
  await Notifications.scheduleNotificationAsync({
    content: { title: 'Bildirim', body: 'Merhaba!' },
    trigger: { seconds: 5 },
  });
};


// iyi
import { Platform } from 'react-native';
import * as Notifications from 'expo-notifications';

const sendNotification = async () => {
  if (Platform.OS === 'ios') {
    await Notifications.requestPermissionsAsync(); //bildirim izni isteniyor
  }

  await Notifications.scheduleNotificationAsync({
    content: { title: 'Bildirim', body: 'Merhaba!' },
    trigger: { seconds: 5 },
  });
};

```


