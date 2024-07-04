# t20_wc_batting_summary

- ## Stage 1: batting page
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
   
- ## Stage 2: Scorecard
   - ### Interaction code:
  
           navigate(input.url);
           collect(parse());
       
   - ### Parser code:

          var match = $('div').filter(function(){
           return $(this)
             .find('span > span > span').text() === String("Match Flow") 
          }).siblings()
                            
          team1 = $(match.eq(0)).find('span > span > span').text().replace(" Innings", "")
          team2 = $(match.eq(1)).find('span > span > span').text().replace(" Innings", "")
          matchInfo = team1 +  ' Vs ' + team2
                            
          var tables = $('div > table.ci-scorecard-table');
          var firstInningRows = $(tables.eq(0)).find('tbody > tr').filter(function(index, element){
          return $(this).find("td").length >= 8
          })
                            
          var secondInningsRows = $(tables.eq(1)).find('tbody > tr').filter(function(index, element){
          return $(this).find("td").length >= 8
          });
          var battingSummary = []
          firstInningRows.each((index, element) => {
          var tds = $(element).find('td');
          battingSummary.push({
                "match": matchInfo,
                "teamInnings": team1,
                "battingPos": index+1,
                "batsmanName": $(tds.eq(0)).find('a > span > span').text().replace(' ', ''),
                "dismissal": $(tds.eq(1)).find('span > span').text(),
                "runs": $(tds.eq(2)).find('strong').text(), 
                "balls": $(tds.eq(3)).text(),
                "4s": $(tds.eq(5)).text(),
                "6s": $(tds.eq(6)).text(),
                "SR": $(tds.eq(7)).text()
          });
          });
                                    
          secondInningsRows.each((index, element) => {
          var tds = $(element).find('td');
           battingSummary.push({
                "match": matchInfo,
                "teamInnings": team2,
                "battingPos": index+1,
                "batsmanName": $(tds.eq(0)).find('a > span > span').text().replace(' ', ''),
                "dismissal": $(tds.eq(1)).find('span > span').text(),
                "runs": $(tds.eq(2)).find('strong').text(), 
                "balls": $(tds.eq(3)).text(),
                "4s": $(tds.eq(5)).text(),
                "6s": $(tds.eq(6)).text(),
                "SR": $(tds.eq(7)).text()
          });
          });
                                    
          return {"battingSummary": battingSummary}