Group: https://t.me/webhiru
Website: datakodehiru.blogspot.com


Rule realtime database: 

  "series": {
        ".read": "auth != null || !data.child('linksPremium').exists()",
        ".write": "auth != null",
        "episodes": {
          "$episodeId": {
            ".read": "auth != null || !data.child('linksPremium').exists()",
            ".write": "auth != null"
          }
        }
    },
      
