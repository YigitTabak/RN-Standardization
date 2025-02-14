- ### Dinamik Stack Navigasyonu Kullanımı
    * Dinamik sayfalar için ekran isimlerini sabit olarak kullanmak yerine, nesne ile tanımlayarak kullanılmalıdır.
    ```jsx
    // kötü
    export default function HomeScreen({ navigation }) {
      return (
          <Button title="Go to Profile" onPress={() => navigation.navigate('Profile', { userId: 123 })} />
        );
    }


    // iyi  
    const SCREENS = {
    Home: 'Home',
    Profile: 'Profile',
    };

    export default function HomeScreen({ navigation }) {
      return (
        <Button title="Go to Profile" onPress={() => navigation.navigate(SCREENS.Profile, { userId: 123 })} />
      );
    }
    ```
  
  ### Dinamik Stack Navigasyonu Kullanımı
    * Farklı navigasyon türleri, her biri kendi işlevine uygun şekilde ayırılarak organize edilmelidir.
    ```jsx
    // kötü
    export default function AppNavigator() {
      return (
        <NavigationContainer>
          <Stack.Navigator>
            <Stack.Screen name="Home" component={HomeScreen} />
            <Stack.Screen name="Profile" component={ProfileScreen} />
            <Stack.Screen name="Settings" component={SettingsScreen} />
          </Stack.Navigator>
        </NavigationContainer>
      );
    }


    // iyi  
        function HomeStack() {
      return (
        <Stack.Navigator>
          <Stack.Screen name="HomeMain" component={HomeScreen} />
          <Stack.Screen name="Profile" component={ProfileScreen} />
        </Stack.Navigator>
      );
    }

    export default function AppNavigator() {
      return (
        <NavigationContainer>
          <Tab.Navigator>
            <Tab.Screen name="HomeTab" component={HomeStack} />
            <Tab.Screen name="Settings" component={SettingsScreen} />
          </Tab.Navigator>
        </NavigationContainer>
      );
    }

    ```    
  ### Navigasyon Animasyonları ve Geçişler
    * Geçiş animasyonları, kullanıcı deneyimini iyileştirmek için akıcı ve bağlama uygun şekilde tasarlanmalıdır.
    ```jsx
    // kötü
    <Stack.Navigator
      screenOptions={{
        transitionSpec: {
          open: { animation: 'timing', config: { duration: 1000 } },
          close: { animation: 'timing', config: { duration: 1000 } },
        },
      }}
    >
      <Stack.Screen name="Home" component={HomeScreen} />
      <Stack.Screen name="Profile" component={ProfileScreen} />
    </Stack.Navigator>


    // iyi  
    <Stack.Navigator
      screenOptions={{
        cardStyleInterpolator: ({ current, layouts }) => ({
          cardStyle: {
            transform: [
              {
                translateX: current.progress.interpolate({
                  inputRange: [0, 1],
                  outputRange: [layouts.screen.width, 0],
                }),
              },
            ],
          },
        }),
      }}
    >
      <Stack.Screen name="Home" component={HomeScreen} />
      <Stack.Screen name="Profile" component={ProfileScreen} />
    </Stack.Navigator>

    ```  
  ### Navigasyon Stack'ini Temizleme
    * Önceki ekranlara geri dönülmemesi gereken senaryolarda, navigasyon stack'i sıfırlanmalıdır.
    ```jsx
    // kötü
    navigation.navigate('Home');


    // iyi
    navigation.reset({ 
      index: 0,
      routes: [{ name: 'Details' }],
    });
 


    ``` 
  ### Deep Linking Desteği 
    * Kullanıcıların uygulamanın belirli bir ekranına doğrudan erişebilmesi için Deep Linking kullanılmaıdır.
    ```jsx
    const linking = {
    prefixes: ['myapp://'],
    config: {
      screens: {
        Home: 'home',
        Details: 'details/:itemId',
      },
    },
  };

    <NavigationContainer linking={linking}>
      {/* Screens */}
    </NavigationContainer>
  

    ``` 
  ### Ekranlar Arasında Parametre Geçişi 
    * Parametreler, type-checking sağlamak için route.params ile okunmalıdır.
    ```jsx
    // kötü
    export default function ProfileScreen({ navigation, route }) {
      const userId = route.params ? route.params.userId : null;
      
      return <Text>User ID: {userId}</Text>;
    }


    // iyi
    export default function ProfileScreen({ route }) {
    const { userId } = route.params;

    return <Text>User ID: {userId}</Text>;
    } 
  

    ```   
  ### Navigasyon İçin Kullanıcı Durumunu Kontrol Etme 
    * Kullanıcı durumu doğrudan ekranda değil, uygun bir üst bileşende yönetilmelidir.
    ```jsx
    // kötü
    export default function HomeScreen({ navigation, user }) {
      return (
        <View>
          {user ? (
            <Button title="Go to Dashboard" onPress={() => navigation.navigate('Dashboard')} />
          ) : (
            <Text>Please log in</Text>
          )}
        </View>
      );
    }


    // iyi  
    export default function HomeScreen({ navigation }) {
      const { user } = useAuth(); // Özel hook ile kullanıcı bilgisini alınıyor
      return (
        <View>
          {user ? (
            <Button title="Go to Dashboard" onPress={() => navigation.navigate('Dashboard')} />
          ) : (
            <Text>Please log in</Text>
          )}
        </View>
      );
    } 
  

    ```       
