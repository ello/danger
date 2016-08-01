repo = github.pr_json[:base][:repo][:full_name]
pr_number = github.pr_json[:number]

# HELPERS
# repo
def repo_has_label(repo, label)
  github.api.labels(repo).any?{|l| l[:name] == label}
end

def add_label_to_repo(repo, label, color)
  github.api.add_label(repo, label, color)
end

# issues
def issue_has_label(repo, pr_number, label)
  github.api.labels_for_issue(repo, pr_number).any?{|l| l[:name] == label}
end

def add_labels_to_issue(repo, pr_number, labels)
  github.api.add_labels_to_an_issue(repo, pr_number, labels)
end

def remove_label_from_issue(repo, pr_number, label)
  github.api.remove_label(repo, pr_number, label)
end

# LABELS
# create tiny
if !repo_has_label(repo, 'tiny')
  add_label_to_repo(repo, 'tiny', 'f7c6c7')
end
# add/remove tiny
issue_has_tiny_label = issue_has_label(repo, pr_number, 'tiny')
if git.lines_of_code < 50 && !issue_has_tiny_label
  add_labels_to_issue(repo, pr_number, ['tiny'])
elsif git.lines_of_code > 50 && issue_has_tiny_label
  remove_label_from_issue(repo, pr_number, 'tiny')
end

# WARNINGS
# make it more obvious that a PR is a work in progress and shouldn't be merged yet
warn('PR is classed as Work in Progress') if github.pr_title.include? 'WIP'
# warn when there is a big PR :metal:
warn('Big PR') if git.lines_of_code > 666

# GENERIC MESSAGING
message('Nice, more deletions than insertions :red_circle:') if git.deletions > git.insertions