# t20_wc_bowling_summary

- ## Stage 1: bowling page
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
                  matchInfo = team1 + ' Vs ' + team2
            
                  var tables = $('div > table.ds-table');
                  var firstInningRows = $(tables.eq(1)).find('tbody > tr').filter(function(index, element){
                    return $(this).find("td").length >= 11
                  })
            
                  var secondInningsRows = $(tables.eq(3)).find('tbody > tr').filter(function(index, element){
                    return $(this).find("td").length >= 11
                  });
            
                  var bowlingSummary = []
                  firstInningRows.each((index, element) => {
                    var tds = $(element).find('td');
                    bowlingSummary.push({
                          "match": matchInfo,
                          "bowlingTeam": team2,
                          "bowlerName": $(tds.eq(0)).find('a > span').text().replace(' ', ''),
                          "overs": $(tds.eq(1)).text(),
                          "maiden": $(tds.eq(2)).text(), 
                          "runs": $(tds.eq(3)).text(),
                          "wickets": $(tds.eq(4)).text(),
                          "economy": $(tds.eq(5)).text(),
                          "0s": $(tds.eq(6)).text(),
                          "4s": $(tds.eq(7)).text(),
                          "6s": $(tds.eq(8)).text(),
                          "wides": $(tds.eq(9)).text(),
                          "noBalls": $(tds.eq(10)).text()
                    });
                  });
            
                  secondInningsRows.each((index, element) => {
                    var tds = $(element).find('td');
                     bowlingSummary.push({
                          "match": matchInfo,
                          "bowlingTeam": team1,
                          "bowlerName": $(tds.eq(0)).find('a > span').text().replace(' ', ''),
                          "overs": $(tds.eq(1)).text(),
                          "maiden": $(tds.eq(2)).text(), 
                          "runs": $(tds.eq(3)).text(),
                          "wickets": $(tds.eq(4)).text(),
                          "economy": $(tds.eq(5)).text(),
                          "0s": $(tds.eq(6)).text(),
                          "4s": $(tds.eq(7)).text(),
                          "6s": $(tds.eq(8)).text(),
                          "wides": $(tds.eq(9)).text(),
                          "noBalls": $(tds.eq(10)).text()
                    });
                  });
            
                  return {"bowlingSummary": bowlingSummary}