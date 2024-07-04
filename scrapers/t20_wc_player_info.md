# t20_wc_bowling_summary

- ## Stage 1: Home page
    - ### Interaction code:
  
            navigate('https://www.espncricinfo.com/records/tournament/team-match-results/icc-men-s-t20-world-cup-2022-23-14450');
            let links = parse().matchSummaryLinks;
            for(let i of links) { 
              next_stage({url: i}) 
            }
    - ### Parser code:
       
            let links = []
            const allRows = $('#main-container > div.ds-relative > div > div.ds-flex.ds-space-x-5 > div.ds-grow > div:nth-child(2) > div > div:nth-child(1) > div.ds-overflow-x-auto.ds-scrollbar-hide > table > tbody > tr');
             allRows.each((index, element) => {
              const tds = $(element).find('td');
              const rowURL = "https://www.espncricinfo.com" +$(tds[6]).find('a').attr('href');
              links.push(rowURL);
             })
            return {
              'matchSummaryLinks': links
            };
   
- ## Stage 2: Player Links
    - ### Interaction code:
  
            navigate(input.url);
        
            let playersData = parse().playersData;
            for(let obj of playersData) { 
              name = obj['name']
              team = obj['team']
              url = obj['link']
              next_stage({name: name, team: team, url: url}) 
            }
       
    - ### Parser code:

            var playersLinks = []
            
            var match = $('div').filter(function(){
                return $(this)
                  .find('span > span > span').text() === String("Match Flow") 
            }).siblings()
            team1 = $(match.eq(0)).find('span > span > span').text().replace(" Innings", "")
            team2 = $(match.eq(1)).find('span > span > span').text().replace(" Innings", "")
            
            //for batting players
            var tables = $('div > table.ci-scorecard-table');
            var firstInningRows = $(tables.eq(0)).find('tbody > tr').filter(function(index, element){
              return $(this).find("td").length >= 8
            })
            
            var secondInningsRows = $(tables.eq(1)).find('tbody > tr').filter(function(index, element){
              return $(this).find("td").length >= 8
            });
            
            firstInningRows.each((index, element) => {
              var tds = $(element).find('td');
              playersLinks.push({
                    "name": $(tds.eq(0)).find('a > span > span').text().replace(' ', ''),
                    "team": team1,
                    "link": "https://www.espncricinfo.com" + $(tds.eq(0)).find('a').attr('href')  
              });
            });
            
            secondInningsRows.each((index, element) => {
              var tds = $(element).find('td');
               playersLinks.push({
                    "name": $(tds.eq(0)).find('a > span > span').text().replace(' ', ''),
                    "team": team2,
                    "link": "https://www.espncricinfo.com" + $(tds.eq(0)).find('a').attr('href')  
              });
            });
            
            //for bowling players 
            
            var tables = $('div > table.ds-table');
            var firstInningRows = $(tables.eq(1)).find('tbody > tr').filter(function(index, element){
              return $(this).find("td").length >= 11
            })
            
            var secondInningsRows = $(tables.eq(3)).find('tbody > tr').filter(function(index, element){
              return $(this).find("td").length >= 11
            });
            
            firstInningRows.each((index, element) => {
              var tds = $(element).find('td');
              playersLinks.push({
                    "name": $(tds.eq(0)).find('a > span').text().replace(' ', ''),
                    "team": team2.replace(" Innings", ""),
                    "link": "https://www.espncricinfo.com" + $(tds.eq(0)).find('a').attr('href')  
                    
              });
            });
            
            secondInningsRows.each((index, element) => {
              var tds = $(element).find('td');
               playersLinks.push({
                    "name": $(tds.eq(0)).find('a > span').text().replace(' ', ''),
                    "team": team1.replace(" Innings", ""),
                    "link": "https://www.espncricinfo.com" + $(tds.eq(0)).find('a').attr('href')
              });
            });
              
            return {"playersData": playersLinks}
             

- ## Stage 3: Player Info
    - ### Interaction code:
  
            navigate(input.url);
            final_data = parse()
            collect(
            {
             "name": input.name,
              "team": input.team,
              "battingStyle": final_data.battingStyle,
              "bowlingStyle": final_data.bowlingStyle,
              "playingRole":  final_data.playingRole,
              "description": final_data.content,
            });
  
    - ### Parser code:
            const battingStyle = $('div.ds-grid > div').filter(function(index){
                return $(this).find('p').first().text() === String('Batting Style')
              })
            
            const bowlingStyle = $('div.ds-grid > div').filter(function(index){
                return $(this).find('p').first().text() === String('Bowling Style')
              })
            
            const playingRole = $('div.ds-grid > div').filter(function(index){
                return $(this).find('p').first().text() === String('Playing Role')
              })
            
            return {
                "battingStyle": battingStyle.find('span').text(),
               "bowlingStyle": bowlingStyle.find('span').text(),
               "playingRole": playingRole.find('span').text(),
               "content": $('div.ci-player-bio-content').find('p').first().text()
            }