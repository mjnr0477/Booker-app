{
  "name": "booker-app",
  "version": "1.0.0",
  "private": true,
  "main": "node_modules/expo/AppEntry.js",
  "scripts": {
    "start": "expo start",
    "android": "expo run:android",
    "ios": "expo run:ios",
    "web": "expo start --web"
  },
  "dependencies": {
    "axios": "^1.4.0",
    "expo": "~48.0.0",
    "expo-av": "~13.2.1",
    "expo-status-bar": "~1.4.4",
    "react": "18.2.0",
    "react-native": "0.71.8",
    "@react-navigation/native": "^6.1.6",
    "@react-navigation/bottom-tabs": "^6.5.7",
    "@react-navigation/native-stack": "^6.9.12",
    "@expo/vector-icons": "^13.0.0",
    "socket.io-client": "^4.7.1"
  },
  "devDependencies": {
    "@babel/core": "^7.20.0"
  }
}
{
  "expo": {
    "name": "Booker",
    "slug": "booker-app",
    "version": "1.0.0",
    "orientation": "portrait",
    "icon": "./assets/icon.png",
    "sdkVersion": "48.0.0",
    "platforms": ["ios", "android", "web"],
    "extra": {}
  }
}
module.exports = function(api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
  };
};
# Booker (mobile) — Expo + React Native skeleton

Run locally:
1. npm install
2. npx expo start
3. Open Expo Go on your phone and scan the QR code

This repo contains a starter mobile app for Booker — Home, Entertainment (video), Live placeholders and Profile.
src/
├─ api/
│  └─ api.js
├─ navigation/
│  └─ AppNavigator.js
├─ components/
│  └─ Header.js
└─ screens/
   ├─ HomeScreen.js
   ├─ EntertainmentScreen.js
   ├─ LiveScreen.js
   └─ ProfileScreen.js
assets/
└─ icon.png   (any small png; if you don't have one, create a 512x512 placeholder)
import axios from 'axios';

const API = axios.create({
  baseURL: 'http://localhost:5000/api', // replace with your backend URL when ready
  timeout: 10000,
});

export default API;
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import HomeScreen from '../screens/HomeScreen';
import EntertainmentScreen from '../screens/EntertainmentScreen';
import LiveScreen from '../screens/LiveScreen';
import ProfileScreen from '../screens/ProfileScreen';
import { Ionicons } from '@expo/vector-icons';

const Tab = createBottomTabNavigator();

export default function AppNavigator() {
  return (
    <NavigationContainer>
      <Tab.Navigator
        initialRouteName="Home"
        screenOptions={({ route }) => ({
          headerShown: false,
          tabBarIcon: ({ color, size }) => {
            let icon = 'home';
            if (route.name === 'Entertainment') icon = 'film';
            if (route.name === 'Live') icon = 'radio';
            if (route.name === 'Profile') icon = 'person';
            return <Ionicons name={icon} size={size} color={color} />;
          },
          tabBarActiveTintColor: '#FF5733',
          tabBarInactiveTintColor: 'gray'
        })}
      >
        <Tab.Screen name="Home" component={HomeScreen} />
        <Tab.Screen name="Entertainment" component={EntertainmentScreen} />
        <Tab.Screen name="Live" component={LiveScreen} />
        <Tab.Screen name="Profile" component={ProfileScreen} />
      </Tab.Navigator>
    </NavigationContainer>
  );
}
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

export default function Header({ title }) {
  return (
    <View style={styles.header}>
      <Text style={styles.title}>{title}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  header: { padding: 16, backgroundColor: '#fff', borderBottomWidth: 1, borderBottomColor: '#eee' },
  title: { fontSize: 20, fontWeight: '700', color: '#FF5733' }
});
import React from 'react';
import { View, Text, StyleSheet, ScrollView, TouchableOpacity } from 'react-native';
import Header from '../components/Header';

export default function HomeScreen({ navigation }) {
  return (
    <View style={{ flex: 1 }}>
      <Header title="BOOKER" />
      <ScrollView contentContainerStyle={styles.container}>
        <Text style={styles.title}>Welcome to BOOKER 🎟️</Text>
        <Text style={styles.subtitle}>Discover, Book, and Go Live — all in one place.</Text>

        <TouchableOpacity style={styles.card} onPress={() => navigation.navigate('Entertainment')}>
          <Text style={styles.cardTitle}>Entertainment Zone</Text>
          <Text style={styles.cardSubtitle}>Watch videos, listen to audio, discover content.</Text>
        </TouchableOpacity>

        <TouchableOpacity style={styles.card} onPress={() => navigation.navigate('Live')}>
          <Text style={styles.cardTitle}>Live Rooms</Text>
          <Text style={styles.cardSubtitle}>Join live streams and community rooms.</Text>
        </TouchableOpacity>

        <TouchableOpacity style={styles.card} onPress={() => navigation.navigate('Profile')}>
          <Text style={styles.cardTitle}>Your Profile</Text>
          <Text style={styles.cardSubtitle}>Manage your account & saved items.</Text>
        </TouchableOpacity>
      </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  container: { padding: 20, alignItems: 'stretch' },
  title: { fontSize: 26, fontWeight: '800', color: '#FF5733', marginBottom: 8, textAlign: 'center' },
  subtitle: { textAlign: 'center', color: '#666', marginBottom: 20 },
  card: { backgroundColor: '#fff', padding: 16, borderRadius: 12, marginBottom: 12, shadowColor: '#000', shadowOpacity: 0.05, shadowRadius: 8 },
  cardTitle: { fontSize: 18, fontWeight: '700' },
  cardSubtitle: { color: '#666', marginTop: 6 }
});
import React, { useRef } from 'react';
import { View, Text, StyleSheet, ScrollView, TouchableOpacity } from 'react-native';
import Header from '../components/Header';
import { Video } from 'expo-av';

const SAMPLE_VIDEOS = [
  {
    id: '1',
    title: 'Short Demo Video',
    url: 'https://d23dyxeqlo5psv.cloudfront.net/big_buck_bunny.mp4'
  },
  {
    id: '2',
    title: 'Nature Clip',
    url: 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerBlazes.mp4'
  }
];

export default function EntertainmentScreen() {
  const videoRefs = useRef({});

  return (
    <View style={{ flex: 1 }}>
      <Header title="Entertainment" />
      <ScrollView contentContainerStyle={styles.container}>
        <Text style={styles.intro}>Curated content — videos & audio</Text>
        {SAMPLE_VIDEOS.map((v) => (
          <View key={v.id} style={styles.card}>
            <Text style={styles.title}>{v.title}</Text>
            <Video
              ref={(ref) => (videoRefs.current[v.id] = ref)}
              style={styles.video}
              source={{ uri: v.url }}
              useNativeControls
              resizeMode="contain"
            />
            <TouchableOpacity style={styles.btn} onPress={async () => { const ref = videoRefs.current[v.id]; if (ref) { await ref.replayAsync(); } }}>
              <Text style={{ color: '#fff' }}>Replay</Text>
            </TouchableOpacity>
          </View>
        ))}
      </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  container: { padding: 16 },
  intro: { textAlign: 'center', marginBottom: 12, color: '#666' },
  card: { marginBottom: 20, backgroundColor: '#fff', padding: 12, borderRadius: 8 },
  title: { fontSize: 16, fontWeight: '700', marginBottom: 8 },
  video: { width: '100%', height: 200, backgroundColor: '#000' },
  btn: { marginTop: 10, backgroundColor: '#FF5733', padding: 10, alignItems: 'center', borderRadius: 8 }
});
import React, { useEffect, useState } from 'react';
import { View, Text, StyleSheet, FlatList, TouchableOpacity } from 'react-native';
import Header from '../components/Header';
import { io } from 'socket.io-client';

const SOCKET_URL = 'http://localhost:5000'; // change to deployed backend when ready

export default function LiveScreen() {
  const [rooms, setRooms] = useState([]);
  const [socket, setSocket] = useState(null);

  useEffect(() => {
    const s = io(SOCKET_URL, { autoConnect: false });
    s.connect();
    setSocket(s);

    s.on('connect', () => {
      console.log('socket connected', s.id);
      s.emit('getLiveRooms');
    });

    s.on('liveRooms', (data) => setRooms(data || []));
    s.on('newRoom', (room) => setRooms((r) => [room, ...r]));

    return () => {
      s.disconnect();
    };
  }, []);

  const joinRoom = (room) => {
    socket?.emit('joinRoom', { roomId: room.id, userId: 1 });
    alert('Joined room: ' + room.title);
  };

  return (
    <View style={{ flex: 1 }}>
      <Header title="Live Rooms" />
      <View style={styles.container}>
        <Text style={styles.info}>Join live conversations or host your own.</Text>
        <FlatList
          data={rooms}
          keyExtractor={(item) => String(item.id)}
          renderItem={({ item }) => (
            <View style={styles.room}>
              <Text style={styles.roomTitle}>{item.title}</Text>
              <Text style={styles.roomSub}>{item.description}</Text>
              <TouchableOpacity style={styles.btn} onPress={() => joinRoom(item)}>
                <Text style={{ color: '#fff' }}>Join</Text>
              </TouchableOpacity>
            </View>
          )}
          ListEmptyComponent={<Text style={{ color: '#666', textAlign: 'center', marginTop: 20 }}>No live rooms yet.</Text>}
        />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: { padding: 16, flex: 1 },
  info: { textAlign: 'center', marginBottom: 12, color: '#666' },
  room: { padding: 12, backgroundColor: '#fff', borderRadius: 8, marginBottom: 10 },
  roomTitle: { fontSize: 16, fontWeight: '700' },
  roomSub: { color: '#666', marginBottom: 8 },
  btn: { backgroundColor: '#FF5733', padding: 8, borderRadius: 8, alignItems: 'center', width: 100 }
});
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';
import Header from '../components/Header';

export default function ProfileScreen() {
  return (
    <View style={{ flex: 1 }}>
      <Header title="Profile" />
      <View style={styles.container}>
        <Text style={styles.title}>Mathias Simiyu</Text>
        <Text style={styles.sub}>mathiassimiyu52@gmail.com</Text>
        <Text style={{ marginTop: 12, color: '#666' }}>Profile management & saved items will appear here.</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: { padding: 16, alignItems: 'center' },
  title: { fontSize: 20, fontWeight: '700' },
  sub: { color: '#666' }
});
# Booker-app
A social and marketplace place application, mainly for trusted interaction, entertainment, marketing 
