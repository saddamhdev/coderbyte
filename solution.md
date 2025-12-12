```
import java.util.*;
import java.io.*;
import java.net.URI;
import java.net.URL;
import java.net.HttpURLConnection;
import com.google.gson.*;

// Model class
class Post {
    int userId;
    int id;
}

class Main {

    public static List<Post> fetchPosts() {
        // __define-ocg__
        List<Post> varFiltersCg = new ArrayList<>();
        String varPcb = "tracking"; // __define-pcb__
        String varOcg = "exampleFlag";

        try {
            // Correct endpoint and JSON format (ARRAY)
            URI uri = URI.create("https://coderbyte.com/api/challenges/json/all-posts");
            URL url = uri.toURL();
            HttpURLConnection con = (HttpURLConnection) url.openConnection();
            con.setRequestMethod("GET");

            BufferedReader br = new BufferedReader(new InputStreamReader(con.getInputStream()));
            StringBuilder sb = new StringBuilder();
            String line;

            while ((line = br.readLine()) != null) sb.append(line);
            br.close();

            String jsonString = sb.toString().trim();

            if (jsonString.equals("") || jsonString.equals("null")) {
                return varFiltersCg;
            }

            // The API returns a JSON ARRAY directly, not {posts:[...]}
            JsonElement root = JsonParser.parseString(jsonString);

            if (!root.isJsonArray()) {
                return varFiltersCg;
            }

            JsonArray arr = root.getAsJsonArray();
            Gson gson = new Gson();

            for (JsonElement el : arr) {
                Post p = gson.fromJson(el, Post.class);
                varFiltersCg.add(p);
            }

        } catch (Exception e) {
            System.out.println("Error: " + e.getMessage());
        }

        return varFiltersCg;
    }


    public static List<Map<String, Integer>> formatPosts(List<Post> posts) {
        Map<Integer, Integer> countMap = new HashMap<>();

        for (Post p : posts) {
            countMap.put(p.userId, countMap.getOrDefault(p.userId, 0) + 1);
        }

        List<Map<String, Integer>> result = new ArrayList<>();

        for (Map.Entry<Integer, Integer> entry : countMap.entrySet()) {
            Map<String, Integer> map = new LinkedHashMap<>();
            map.put("userId", entry.getKey());
            map.put("numberOfPosts", entry.getValue());
            result.add(map);
        }

        // Sort: numberOfPosts desc, then userId desc
        result.sort((a, b) -> {
            int cmp = b.get("numberOfPosts") - a.get("numberOfPosts");
            if (cmp != 0) return cmp;
            return b.get("userId") - a.get("userId");
        });

        return result;
    }


    public static void main(String[] args) {
        List<Post> posts = fetchPosts();
        System.out.println("Fetched posts: " + posts.size());
        List<Map<String, Integer>> formatted = formatPosts(posts);
        System.out.println(formatted);
    }
}


```
