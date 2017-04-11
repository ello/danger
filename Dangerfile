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
labels = [
  {name: 'tiny', color: 'f7c6c7'},
  {name: 'please review', color: 'fbca04'},
  {name: 'reviewed', color: '0e8a16'},
  {name: 'wip', color: '1d76db'},
  {name: 'release', color: '336694'},
]
labels.each do |lbl|
  unless repo_has_label?(repo, lbl[:name])
    add_label_to_repo!(repo, lbl[:name], lbl[:color])
  end
end

if github.pr_title.downcase.start_with? 'release'
  # add/remove release label
  is_release = true
  if !issue_has_label?(repo, pr_number, 'release')
    add_labels_to_issue!(repo, pr_number, ['release'])
  end
else
  is_release = false

  # add/remove tiny
  issue_has_tiny_label = issue_has_label?(repo, pr_number, 'tiny')
  if git.lines_of_code < 50 && !issue_has_tiny_label
    add_labels_to_issue!(repo, pr_number, ['tiny'])
  elsif git.lines_of_code > 50 && issue_has_tiny_label
    remove_label_from_issue!(repo, pr_number, 'tiny')
  end

  # add/remove please review
  if !issue_has_label?(repo, pr_number, 'reviewed') && !issue_has_label?(repo, pr_number, 'please review')
    add_labels_to_issue!(repo, pr_number, ['please review'])
  end
end

# add/remove wip
if github.pr_title.include? 'WIP'
  add_labels_to_issue!(repo, pr_number, ['wip'])
elsif issue_has_label?(repo, pr_number, 'wip')
  remove_label_from_issue!(repo, pr_number, 'wip')
end

# WARNINGS
# warn when there is a big PR :metal:
warn('Big PR', sticky: false) if git.lines_of_code > 666 && !is_release
# warn when there is no tracker story tagged for this PR
warn('Please provide a linked tracker story for this PR in the description', sticky: false) unless github.pr_body.match(/(https?:\/\/)?(w{3}\.)?pivotaltracker\.com\/story\/show\/\d*/)

# GENERIC MESSAGING
message('Nice, more deletions than insertions :red_circle:', sticky: false) if git.deletions > git.insertions
