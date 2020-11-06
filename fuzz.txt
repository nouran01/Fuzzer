import mechanicalsoup
import requests
import argparse
from urllib.parse import urlparse

parser = argparse.ArgumentParser()
parser.add_argument("tool")
parser.add_argument("url")
parser.add_argument("--custom-auth")
parser.add_argument("--common-words")

args=parser.parse_args()
browser = mechanicalsoup.StatefulBrowser()
session = requests.Session()

def page_response(word):
    return str(browser.open(args.url+word))=="<Response [200]>" 

def crawl (url,domain,listOfUrls,crawledUrls):

    crawledUrls.append(url)
    browser.open(url)
    links = browser.links()
    
    for link  in links:
        href = link.get("href")
        absolute = browser.absolute_url(href)
        if (urlparse(absolute).netloc == domain) and (absolute not in listOfUrls):
            listOfUrls.append(absolute)

    
    for page_link in listOfUrls:
        
        if (urlparse(page_link).netloc == domain) and (page_link not in crawledUrls) and ("pdf" not in page_link):
            
            crawl(page_link,domain,listOfUrls,crawledUrls)
      
    return crawledUrls
        
def guess_links(url,fileName):
    gURL = []
    extensions = [".php",".jsp",".html"]
    with open(fileName,"r")as f:
        for word in f:
            for ext in extensions:
                if page_response(word.strip("\n")+ext):
                    gURL.append(args.url+word.strip("\n")+ext)

    return gURL

def parseURL (links):
    inputs = {}
    for l in links:
        parse_l = urlparse(l)
        q=parse_l.query
        if q !="":
            inputs[l] = q
    return inputs
    
def dvwa_login(): 
    browser.open(args.url)
    browser.select_form('form[action="login.php"]')
    browser["username"]="admin"
    browser["password"]="password"
    page_resp = browser.submit_selected()
    return page_resp

if __name__ == "__main__":
    if args.custom_auth is not None:
        args.custom_auth = args.custom_auth.lower()

    if args.custom_auth == "dvwa":

        browser.open(args.url)
        '''Checking the page source before entering username/password'''
    ##    print("\nThe HTML of the login page (before entering username/password):")
    ##    url = browser.get_url()
    ##    req = requests.get(url)
    ##    print(req.text)
    ##    
    ##    print('\n#################################################\n')

        '''Printing the page source after entering username/password'''

        page_resp = dvwa_login()
        print("\n")
        print("logged in successfully!", "\nThe HTML of the new page after logging in:")
        print("="*100)
        print(page_resp.text)
        
        print("="*100)

    if args.tool.lower() == "discover":
        url = args.url
        browser.open(url)

        '''Guessing pages from a common words file'''
        if args.common_words is None:
            file_name = "mywords.txt"
        else:
            file_name = args.common_words

        gl = guess_links(url,file_name)
        

        '''Crawling'''
        print("\nLINKS FOUND ON PAGE:")
        print("="*100)
        
        urlsOnPg = []+gl
        crawled = []
        parsed_url = urlparse(url)
        domain = parsed_url.netloc
        linksOnPg = crawl(url,domain,urlsOnPg,crawled)
        for link in linksOnPg:
            print(link)

        print("="*100)

        '''Printing Successfully guessed pages'''
        print("\nLINKS SUCCESSFULLY GUESSED:")
        print("="*100)

        for gpage in gl:
            print(gpage)
        
        print("="*100)

        '''Parsing urls to record inputs that can be fuzzed'''
        parsedURLS = parseURL(linksOnPg)
        print("\nParsed URLs:")
        print("="*100)
        
        for u in parsedURLS:
            print("URL:",u,"\n\tInput:",parsedURLS[u])
            
        print("="*100)

        '''Printing input forms on pages'''
        print("\nINPUT FORMS ON PAGES:")
        print("="*100)
                               
        if args.custom_auth == "dvwa":
            dvwa_login()
        for link in linksOnPg:
            browser.open(link)
            for f in browser.get_current_page().find_all("form"):
                title = browser.get_current_page().title.text
                print(title[:title.index("::")])
                print("URL: ",link)
                for Input_feild in f.find_all('input'):
                    if Input_feild.has_attr('name') and 'submit' not in Input_feild.attrs['type']:
                        print("\t"+Input_feild['name'])
            if  args.custom_auth == "dvwa" and "logout.php" in link: 
                dvwa_login()


        print("="*100)

        '''Printing cookies'''
        print("\nCOOKIES")
        print("="*100)
        cookies = []
        cookies = browser.get_cookiejar()
        for cookie in cookies:
            print(cookie)
        print("="*100)
        

        
    
    
                   

    
        
            
        









































    



