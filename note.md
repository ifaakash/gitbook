# NOTE

```
curl \
  -H "Content-Type: application/json" \
  -d "{\"contents\":[{\"parts\":[{\"text\":\"Explain how AI works\"}]}]}" \
  -X POST "https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash-latest:generateContent?key=YOUR_API_KEY"  

```

AIzaSyDDQBzaFSaRFgf4DN81vEYSxCIDOAGhTek



ok. i have made the changes for this. now when i click on any of this genre, this will hit the gemini API which i will give you below and then provide me output.

url\
-H "Content-Type: application/json"\
-d "{"contents":\[{"parts":\[{"text":"Give me list of 10 """}]}]}"\
-X POST "https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash-latest:generateContent?key=YOUR\_API\_KEY"
