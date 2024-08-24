### Required firebase realtime database rules
```
"bookmarks_history": {
      "$uid": {
        ".read": "auth != null && auth.uid === $uid",
        ".write": "auth != null && auth.uid === $uid"
      }
    },  
```
