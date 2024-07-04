- ## 1.  t20_wc_match_results
   - Interaction code:
        
            navigate('https://www.espncricinfo.com/records/season/team-match-results/2022to23-2022to23?trophy=89');
            
            collect(parse());
   - Parser code:
     
          let matchsummary = [];
          const allRows = $('#main-container > div.ds-relative > div > div.ds-flex.ds-space-x-5 > div.ds-grow > div:nth-child(2) > div > div:nth-child(1) > div.ds-overflow-x-auto.ds-scrollbar-hide > table > tbody > tr');
                              
          allRows.each((index, ele) => {
            const tds = $(ele).find('td');
            matchsummary.push({
              'team1': $(tds[0]).text(),
              'team2': $(tds[1]).text(),
              'winner': $(tds[2]).text(),
              'margin': $(tds[3]).text(),
              'ground': $(tds[4]).text(),
              'matchDate': $(tds[5]).text(),
              'scorecard': $(tds[6]).text()
            });
          });
            
          return {
            "matchSummary": matchsummary,
          }
