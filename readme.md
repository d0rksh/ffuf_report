# Fuff Report (View & Render)

## Installation
1. git clone https://github.com/ffuf/ffuf.git
2. cd ffuf/pkg/output
3. open file_html.go file in code editor
4. go to line 20 ```const htmlTemplate = "paste_here"```
5. copy the template.tp and replace ```const htmlTemplate```
6. go to line 12 ```type htmlFileOutput struc {...}``` replace with below
```
type htmlFileOutput struct {
	CommandLine string
	Time        string
	Keys        []string
	Results     []ffuf.Result
	Target      string
}
```
7. go to line 301  ``` func writeHTML(filename string, config *ffuf.Config, results []ffuf.Result) error ``` paste the below code at the start of writeHTML function
```
	u, _ := url.Parse(config.Url)
	domain := u.Scheme + "://" + u.Hostname()
	results = colorizeResults(results)

	ti := time.Now()

	keywords := make([]string, 0)
	for _, inputprovider := range config.InputProviders {
		keywords = append(keywords, inputprovider.Keyword)
	}

	outHTML := htmlFileOutput{
		CommandLine: config.CommandLine,
		Time:        ti.Format(time.RFC3339),
		Results:     results,
		Keys:        keywords,
		Target:      domain,
	}

	f, err := os.Create(filename)
	if err != nil {
		return err
	}
	defer f.Close()

	templateName := "output.html"
	t := template.New(templateName).Delims("{{", "}}")
	_, err = t.Parse(htmlTemplate)
	if err != nil {
		return err
	}
	err = t.Execute(f, outHTML)
	return err
```

# Output/Build
1. go build
2. sudo cp ffuf /usr/bin or /usr/local/bin
3. ```ffuf -u 'https://target.com/FUZZ' -w word -of html -o index.html -od .``` (only supports current directory . (dot) )
4. run server ```twistd -no web --port tcp:80  --path=.``` (python's SimpleHTTPServer) can't handle large file size so please use twistd or other alternative
6. it won't work on file:// protocol





## Screenshots

![alt text](https://raw.githubusercontent.com/d0rksh/ffuf_report/main/ffuf_1.png)
![alt text](https://raw.githubusercontent.com/d0rksh/ffuf_report/main/ffuf_2.png)
![alt text](https://raw.githubusercontent.com/d0rksh/ffuf_report/main/fuff_3.png)
