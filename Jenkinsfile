job('upstreamJob') {
    scm {
        git {
            remote {
                github('magento/devdocs-mbi')
                refspec('+refs/pull/*:refs/remotes/origin/pr/*')
            }
            branch('${sha1}')
        }
    }

    triggers {
        pullRequest {
            admin('jeff-matthews')
            // admins(['user_2', 'user_3'])
            // userWhitelist('you@you.com')
            // userWhitelist(['me@me.com', 'they@they.com'])
            // orgWhitelist('my_github_org')
            // orgWhitelist(['your_github_org', 'another_org'])
            cron('H/2 * * * *')
            triggerPhrase('running tests')
            onlyTriggerPhrase('true')
            useGitHubHooks('false')
            permitAll('false')
            autoCloseFailedPullRequests('false')
            displayBuildErrorsOnDownstreamBuilds('false')
            whiteListTargetBranches(['jenkins-test','test', 'test2'])
            // allowMembersOfWhitelistedOrgsAsAdmin()
            // extensions {
               // commitStatus {
                 //   context('deploy to staging site')
                   // triggeredStatus('starting deployment to staging site...')
                   // startedStatus('deploying to staging site...')
                   // addTestResults(true)
                   // statusUrl('http://mystatussite.com/prs')
                   // completedStatus('SUCCESS', 'All is well')
                   // completedStatus('FAILURE', 'Something went wrong. Investigate!')
                   // completedStatus('PENDING', 'still in progress...')
                   // completedStatus('ERROR', 'Something went really wrong. Investigate!')
                // }
            // }
        }
    }
    publishers {
        mergeGithubPullRequest {
            mergeComment('merged by Jenkins')
            onlyAdminsMerge('true')
            disallowOwnCode('false')
            failOnNonMerge('false')
            deleteOnMerge('true')
        }
    }
}
