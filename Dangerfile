REPO = github.pr_json[:base][:repo][:full_name]
PR_NUMBER = github.pr_json[:number]

# HELPERS
# repo
def repo_has_label?(label)
  github.api.labels(REPO).any? { |l| l[:name] == label }
end

def add_label_to_repo!(label, color)
  github.api.add_label(REPO, label, color)
end

# issues
def issue_has_label?(label)
  github.api.labels_for_issue(REPO, PR_NUMBER).any? { |l| l[:name] == label }
end

def add_label_to_issue!(label)
  github.api.add_labels_to_an_issue(REPO, PR_NUMBER, [label])
end

def remove_label_from_issue!(label)
  github.api.remove_label(REPO, PR_NUMBER, label)
end

# LABELS
labels = [
  {name: 'tiny', color: 'f7c6c7'},
  {name: 'please review', color: 'fbca04'},
  {name: 'reviewed', color: '0e8a16'},
  {name: 'wip', color: '1d76db'},
  {name: 'release', color: '336694'},
]
labels.each do |lbl|
  unless repo_has_label?(lbl[:name])
    add_label_to_repo!(lbl[:name], lbl[:color])
  end
end

if github.pr_title.downcase.start_with? 'release'
  # add/remove release label
  is_release = true
  if !issue_has_label?('release')
    add_label_to_issue!('release')
  end
else
  is_release = false

  # add/remove tiny
  issue_has_tiny_label = issue_has_label?('tiny')
  if git.lines_of_code < 50 && !issue_has_tiny_label
    add_label_to_issue!('tiny')
  elsif git.lines_of_code > 50 && issue_has_tiny_label
    remove_label_from_issue!('tiny')
  end

  # add/remove please review
  if !issue_has_label?('reviewed') && !issue_has_label?('please review')
    add_label_to_issue!('please review')
  end
end

# add/remove wip
if github.pr_title.include? 'WIP'
  add_label_to_issue!('wip')
elsif issue_has_label?('wip')
  remove_label_from_issue!('wip')
end

# WARNINGS
# warn when there is a big PR :metal:
warn('Big PR', sticky: false) if git.lines_of_code > 666 && !is_release
# warn when there is no tracker story tagged for this PR
warn('Please provide a linked tracker story for this PR in the description', sticky: false) unless github.pr_body.match(/(https?:\/\/)?(w{3}\.)?pivotaltracker\.com\/story\/show\/\d*/)

# GENERIC MESSAGING
message('Nice, more deletions than insertions :red_circle:', sticky: false) if git.deletions > git.insertions
