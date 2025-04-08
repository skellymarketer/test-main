import requests
import networkx as nx
import matplotlib.pyplot as plt


sample_users = ["arvindr21", "vinitkumar", "gaearon", "addyosmani", "mdo"]
GITHUB_TOKEN = "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"

headers={
    "Authorization": f"Bearer {GITHUB_TOKEN}",
    "Accept": "application/vnd.github.v3+json",
    "User-Agent": "InterestGraph",
    }

def get_starred(username):
    response= requests.get(f"https://api.github.com/users/{username}/starred?per_page=100", headers=headers)
    return [repo["full_name"] for repo in response.json()] if response.status_code == 200 else []


user_repos= {user: get_starred(user) for user in sample_users}
G= nx.Graph();

edge_labels={}
for user1 in user_repos:
    for user2 in user_repos:
        if user1 != user2:
            common_repos= set(user_repos[user1]) & set(user_repos[user2])
            if common_repos:
                G.add_edge(user1, user2)
                edge_labels[(user1, user2)]= "\n".join(list(common_repos) [:2])



pos= nx.spring_layout(G)
nx.draw(G, pos, with_labels= True)
nx.draw_networkx_edge_labels(G, pos, font_color='red', edge_labels=edge_labels, font_size= 5)
plt.show()
