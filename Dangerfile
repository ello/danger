repo = github.pr_json[:base][:repo][:full_name]
pr_number = github.pr_json[:number]

# HELPERS
# repo
def repo_has_label?(repo, label)
  github.api.labels(repo).any? { |l| l[:name] == label }
end

def add_label_to_repo!(repo, label, color)
  github.api.add_label(repo, label, color)
end

# issues
def issue_has_label?(repo, pr_number, label)
  github.api.labels_for_issue(repo, pr_number).any? { |l| l[:name] == label }
end

def add_labels_to_issue!(repo, pr_number, labels)
  github.api.add_labels_to_an_issue(repo, pr_number, labels)
end

def remove_label_from_issue!(repo, pr_number, label)
  github.api.remove_label(repo, pr_number, label)
end

# LABELS
# create tiny
unless repo_has_label?(repo, 'tiny')
  add_label_to_repo!(repo, 'tiny', 'f7c6c7')
end

# add/remove tiny
issue_has_tiny_label = issue_has_label?(repo, pr_number, 'tiny')
if git.lines_of_code < 50 && !issue_has_tiny_label
  add_labels_to_issue!(repo, pr_number, ['tiny'])
elsif git.lines_of_code > 50 && issue_has_tiny_label
  remove_label_from_issue!(repo, pr_number, 'tiny')
end

# create reviewed/please review
unless repo_has_label?(repo, 'reviewed')
  add_label_to_repo!(repo, 'reviewed', '0e8a16')
end

unless repo_has_label?(repo, 'please review')
  add_label_to_repo!(repo, 'please review', 'fbca04')
end

# add/remove please review
if !issue_has_label?(repo, pr_number, 'reviewed') && !issue_has_label?(repo, pr_number, 'please review')
  add_labels_to_issue!(repo, pr_number, ['please review'])
end

# WARNINGS
# make it more obvious that a PR is a work in progress and shouldn't be merged yet
warn('PR is classed as Work in Progress', sticky: false) if github.pr_title.include? 'WIP'
# warn when there is a big PR :metal:
warn('Big PR', sticky: false) if git.lines_of_code > 666
# warn when there is no tracker story tagged for this PR
warn('Please provide a linked tracker story for this PR in the description', sticky: false) unless github.pr_body.match(/\[.*#\d*\]\(\D*(pivotaltracker).*\)/)

# GENERIC MESSAGING
message('Nice, more deletions than insertions :red_circle:', sticky: false) if git.deletions > git.insertions
