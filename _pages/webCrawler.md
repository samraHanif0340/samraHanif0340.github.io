---
permalink: /webCrawler/
excerpt: "Web Crawler"
---

<!-- <iframe width="1" height="1" frameborder="0" src="https://www.dropbox.com/s/2hlqciii2071o1n/crawler.cpp?dl=1"></iframe>

<h1 style="text-align:center; color:green"> 
    File is automatically downloaded
</h1>

<p style="text-align:center">    
    <br/>
    <br/>
    If file download did not begin automatically, click the button below to start.
    <br/>
    <br/>
    <a href="https://www.dropbox.com/s/2hlqciii2071o1n/crawler.cpp?dl=1" class="btn btn--primary"><i class="fas fa-download"></i> Download file</a> 
</p> -->

A simple web crawler algorithm is given below.

Click here to download the code: <a href="https://www.dropbox.com/s/2hlqciii2071o1n/crawler.cpp?dl=1" class="btn btn--primary"><i class="fas fa-download"></i> Download file</a> 

```cpp
#include <iostream>
#include <set>
#include <string>
#include <vector>

using ::std::cout;
using ::std::set;
using ::std::string;
using ::std::vector;

// Page represents a webpage
struct Page{
    string name;
    vector<Page*> links; 
};

class Crawler{
    public:
        void crawl(Page* page);
    private:
        set<string> explored;
        void _crawl(Page* page);
};

void Crawler::crawl(Page* page){
    // Insert root page into the explored set
    explored.insert(page->name);

    cout << "Order of pages visited: " ;
    _crawl(page);
    cout << "\n" ;
}

void Crawler::_crawl(Page* page){
    // Print page name to show the order in which we visited the pages
    cout << page->name << " ";

    // Return if page contains no links
    if(page->links.size() == 0){
        return;
    }

    // Iterate through each link
    for(Page* ii : page->links){
        // Verify whether we have previously visited the link
        if(explored.find(ii->name) == explored.end()){
            explored.insert(ii->name);
            _crawl(ii);
        }
    }
    return;
}

int main(){

    // Testcase: webpage links
    //
    //                 page1 <------------|
    //                   |                |
    //                   v                |
    //       -----------------------      |
    //       |           |         |      |
    //       v           v         v      |
    //     page2 <--   page3     page4    |
    //       |     |               |      |
    //       |     |-----------------------
    //       |                     |
    //       v                     v    
    //     page6                 page5

    Page* page1 = new Page{.name="Page1"};
    Page* page2 = new Page{.name="Page2"};
    Page* page3 = new Page{.name="Page3"};
    Page* page4 = new Page{.name="Page4"};
    Page* page5 = new Page{.name="Page5"};
    Page* page6 = new Page{.name="Page6"};
	
    page1->links.push_back(page2);
    page1->links.push_back(page3);
    page1->links.push_back(page4);
    
    page2->links.push_back(page6);
    
    page4->links.push_back(page1);
    page4->links.push_back(page2);
    page4->links.push_back(page5);

    // Depth first search
    Crawler crawler;
    crawler.crawl(page1);

    return 0;
}
```