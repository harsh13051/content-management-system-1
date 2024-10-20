# content-management-system-1
## code

#include <iostream>
#include <fstream>
#include <cstring>

const int MAX_ARTICLES = 100;

class Article {
public:
    virtual void display() const = 0;
    virtual const char* getType() const = 0;
    virtual void save(std::ofstream& file) const = 0;
    virtual ~Article() {}
};

class TextArticle : public Article {
private:
    int id;
    char title[100];
    char content[500];

public:
    TextArticle(int id, const char* title, const char* content) {
        this->id = id;
        strncpy(this->title, title, 100);
        strncpy(this->content, content, 500);
    }

    void display() const override {
        std::cout << "ID: " << id << "\nTitle: " << title
                  << "\nContent: " << content << "\n" << std::endl;
    }

    const char* getType() const override {
        return "Text Article";
    }

    void save(std::ofstream& file) const override {
        file << title << "|" << content << "\n";
    }
};

class CMS {
private:
    Article* articles[MAX_ARTICLES];
    int articleCount = 0;
    int nextId = 1;
    const char* filename = "articles.txt";

    void loadArticles() {
        std::ifstream file(filename);
        if (!file) {
            std::cerr << "Failed to open file: " << filename << std::endl;
            return;
        }

        char title[100], content[500];
        while (file.good() && articleCount < MAX_ARTICLES) {
            file.getline(title, 100, '|');
            file.getline(content, 500);
            if (file.gcount() > 0) {
                articles[articleCount] = new TextArticle(nextId++, title, content);
                articleCount++;
            }
        }
        file.close();
    }

    void saveArticles() {
        std::ofstream file(filename);
        if (!file) {
            std::cerr << "Failed to open file: " << filename << std::endl;
            return;
        }

        for (int i = 0; i < articleCount; i++) {
            articles[i]->save(file);
        }
        file.close();
    }

public:
    CMS() {
        loadArticles();
    }

    ~CMS() {
        saveArticles();
        for (int i = 0; i < articleCount; i++) {
            delete articles[i];
        }
    }

    void createArticle(const char* title, const char* content) {
        if (articleCount >= MAX_ARTICLES) {
            std::cout << "Cannot create more articles." << std::endl;
            return;
        }
        articles[articleCount] = new TextArticle(nextId++, title, content);
        articleCount++;
        std::cout << "Article created: " << title << std::endl;
    }

    void readArticles() const {
        if (articleCount == 0) {
            std::cout << "No articles available." << std::endl;
            return;
        }

        for (int i = 0; i < articleCount; i++) {
            articles[i]->display();
        }
    }

    void updateArticle(int id, const char* title, const char* content) {
        std::cout << "Update functionality not implemented." << std::endl;
    }

    void deleteArticle(int id) {
        std::cout << "Delete functionality not implemented." << std::endl;
    }
};

int main() {
    CMS cms;
    int choice;
    char title[100], content[500];
    int id;

    while (true) {
        std::cout << "\nContent Management System\n";
        std::cout << "1. Create Article\n";
        std::cout << "2. Read Articles\n";
        std::cout << "3. Update Article\n";
        std::cout << "4. Delete Article\n";
        std::cout << "5. Exit\n";
        std::cout << "Choose an option: ";
        std::cin >> choice;
        std::cin.ignore();

        switch (choice) {
            case 1:
                std::cout << "Enter title: ";
                std::cin.getline(title, 100);
                std::cout << "Enter content: ";
                std::cin.getline(content, 500);
                cms.createArticle(title, content);
                break;
            case 2:
                cms.readArticles();
                break;
            case 3:
                std::cout << "Enter article ID to update: ";
                std::cin >> id;
                std::cin.ignore();
                std::cout << "Enter new title: ";
                std::cin.getline(title, 100);
                std::cout << "Enter new content: ";
                std::cin.getline(content, 500);
                cms.updateArticle(id, title, content);
                break;
            case 4:
                std::cout << "Enter article ID to delete: ";
                std::cin >> id;
                cms.deleteArticle(id);
                break;
            case 5:
                std::cout << "Exiting the CMS." << std::endl;
                return 0;
            default:
                std::cout << "Invalid option. Try again." << std::endl;
        }
    }

    return 0;
}
...

